# Where the data actually lives

A deletion request touches at least seven stores, and the one your ORM knows about is only the first. Everything after it is a copy, a derivation, or someone else's log on someone else's clock. Write the list down before you write the delete.

## The seven

**1. The primary record.** The user row, the uploaded files, the object storage keys, the join tables. Ordinary CRUD. This is the part that is already handled and the part people mistake for the whole job.

**2. Prompt and trace logs.** Request and response bodies in the application log, spans in the tracing backend, the LLM gateway's own log, the error tracker's captured payload, the message archive your support tool keeps. These carry raw prompt text, which means they carry whatever the user typed, including the thing they are now asking you to delete. Retention here is set by a sampling and TTL policy, not by a delete endpoint.

**3. Provider-side retention.** Your model provider keeps its own copy on its own schedule and you can choose the setting, not the clock. OpenAI's platform documentation states that "abuse monitoring logs are generated for all API feature usage and retained for up to 30 days, unless longer retention is required by law, or is reasonably necessary to protect our services or any third party from harm", and that approved customers can get zero data retention. The deliverable is knowing which of those two applies to your account and being able to say so.

**4. Eval and fine-tune datasets.** A golden set snapshotted from production traffic. A JSONL uploaded for a fine-tune six months ago, still sitting in the provider's file store. A regression fixture someone pasted into the repo because it reproduced a bug. These are the copies with the longest half-life because nobody treats them as a datastore.

**5. Derived stores.** Vector index entries, cache keys, search documents, warehouse and lakehouse tables, feature store rows, materialised views, per-user summaries the product generates nightly.

**6. Backups and snapshots.** Database snapshots, object storage versions, WORM-locked archives, the staging environment someone seeded from a production dump.

**7. Trained weights.** Any model you fine-tuned on this data.

## The column that decides everything

For each store, record three things: which identifier links a row to the user, who owns the delete path, and how long that path takes.

The stores that will bite you are the ones with no answer to the first question. A trace keyed only by `request_id` with the user id stripped at ingest. An eval row copied without provenance. A warehouse table where the join key was hashed with a salt that has since rotated. If a store has no key back to the user, deletion in that store is not a query. It is a rebuild, an expiry, or a full-table exclusion, and each of those has a lead time you have to disclose rather than discover later.

Chapters two through four cover the stores where the answer is not ordinary CRUD. What counts as personal data and who the erasure right binds are questions for the privacy-laws reference, not this one.
