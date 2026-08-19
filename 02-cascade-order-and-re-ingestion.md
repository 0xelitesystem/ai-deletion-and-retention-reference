# Cascade order, and why the record comes back

Delete the derivations before the source record, never after, and freeze the ingest path before either. Every derived store has a rebuild path that reads the source, so a rebuild that fires in the gap between the two deletes re-derives the record from data that has not been deleted yet and writes it back. The delete was correct. The order was not.

## The order

1. **Add the subject to a suppression list.** A tombstone keyed by a stable identifier that every ingest, rebuild, backfill and restore job checks before it writes. This is step one, and it is the step that gets skipped.
2. **Delete the derivations.** Vector index entries and their payload text, cache keys, search documents, warehouse rows, feature store rows, generated summaries.
3. **Delete the source record.**
4. **Verify after the next full rebuild cycle**, not on the day.

## The failure, concretely

Nightly ETL runs at 02:00 against a read replica. The replica lagged, or the job reads a snapshot taken at 01:00. The source row is deleted at 01:30 and the vector row at 03:00. At 02:00 the job re-embeds the record from the 01:00 snapshot and writes a fresh vector. At 03:00 you delete the vector that existed when you started and leave the one the job wrote at 02:00. Every store reports success. The record is still searchable in the morning.

The same shape appears in a dozen places. A change-data-capture stream with at-least-once delivery replays a batch. A dead-letter queue is re-driven a week later by someone clearing an alert. A backfill is run to repair an unrelated column and re-derives every row in range. A staging environment is reseeded from a production dump. An analytics pipeline reconstructs a dimension table from an append-only event log that was never in your delete plan.

This is why the common deletion bug is re-ingestion rather than incomplete deletion. The SQL was right and it ran. Something wrote the row again afterwards. A test that queries each store immediately after the delete will pass and prove nothing.

## The suppression list

It has to outlive the record it suppresses, which means it cannot store the record. Keep the minimum that lets a job recognise a match: the internal user id, plus a keyed hash of any natural identifier such as email, plus the request date. It is itself a record of the request and belongs in your retention schedule with a stated purpose. Every job that writes derived data checks it; the restore runbook replays it after a restore completes.

The legal framing recognises copies rather than only originals. Article 17(2) of the GDPR requires a controller that made personal data public to take "reasonable steps, including technical measures, to inform controllers which are processing the personal data that the data subject has requested the erasure by such controllers of any links to, or copy or replication of, those personal data". That provision is about other controllers and published data, not your internal ETL, but the object it names is the right one: links, copies, replications.

## The verification you actually record

Run the identifier through every store's read path on the day, then run the same check after the next full rebuild window has passed, and keep both results. One check proves the delete executed. Two checks separated by a rebuild prove nothing put it back.
