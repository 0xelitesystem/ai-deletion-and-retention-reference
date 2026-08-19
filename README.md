# AI Deletion and Retention Reference

Where user data actually lives in an AI stack, what deleted honestly means in each store, and the two places a delete request cannot reach: trained weights and immutable backups.

## The core idea

Honouring a deletion request in an AI stack means deleting everything derived from the record before the record itself, then writing down the two stores where you did not delete anything. The derived stores are in scope because derivations carry identifiers, not only statistics: Morris et al. (EMNLP 2023) report recovering 89 percent of full names from GTR-embedded MIMIC-III clinical notes, and their stated conclusion is that embeddings should be treated as highly sensitive private data and protected in the same way as raw text. Order matters more than coverage, because every derived store has a rebuild path that reads the source, so a nightly job running between the two deletes puts the record back. The two stores that cannot honour a delete at all are trained weights, where the honest remedy is exclusion from the next training run plus a documented residual, and immutable backups, where the honest remedy is a documented rotation window plus a suppression list that survives a restore.

## Contents

- [01-where-the-data-actually-lives.md](01-where-the-data-actually-lives.md) the full inventory a delete request touches, from the user row to provider-side logs to eval and fine-tune datasets, and how to tell which stores have no key back to the user.
- [02-cascade-order-and-re-ingestion.md](02-cascade-order-and-re-ingestion.md) why derived data is deleted first, the suppression list that has to exist before step one, and the rebuild windows that resurrect a correctly deleted record.
- [03-vector-indexes-are-derived-personal-data.md](03-vector-indexes-are-derived-personal-data.md) the embedding inversion numbers with all three of their conditions, the threat model they require, and why a delete call on an ANN index is often a tombstone.
- [04-weights-and-backups-the-two-you-cannot-delete.md](04-weights-and-backups-the-two-you-cannot-delete.md) what certified removal actually covers, why unlearning is not a deletion claim, and the documented residual and rotation window that are the honest answers instead.

## The stance

Legal definitions are not repeated here; they live in the neighbouring privacy-laws reference. This one is the engineering path: the order of operations, the store list, and the wording you can defend. A residual you disclose with a date beats a deletion claim you cannot support. This is general engineering information, not legal advice.

## Related

- [data-privacy-laws-basics-reference](https://github.com/0xelitesystem/data-privacy-laws-basics-reference) what the erasure right is and who it binds.
- [embeddings-and-vector-search-basics-reference](https://github.com/0xelitesystem/embeddings-and-vector-search-basics-reference) what an embedding and an ANN index are, before you have to delete from one.
- [backup-and-recovery-reference](https://github.com/0xelitesystem/backup-and-recovery-reference) retention schedules and restore drills, which are the machinery chapter four depends on.
- [logging-and-observability-basics-reference](https://github.com/0xelitesystem/logging-and-observability-basics-reference) log retention, sampling and redaction for the trace stores in chapter one.
- [fine-tuning-vs-rag-vs-prompting-reference](https://github.com/0xelitesystem/fine-tuning-vs-rag-vs-prompting-reference) which of the three puts user text into weights and which keeps it in a store you can delete.

## License

MIT. Copyright 0xelitesystem 2026.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).
