# Vector indexes are derived personal data

An embedding of a person's text is personal data and belongs in the deletion path, because inversion research recovers identifiers from embeddings, not only topics.

## The number that matters for deletion

On GTR-embedded clinical notes from the MIMIC-III database, Morris et al. report: "Vec2Text is able to recover 94% of first names, 95% of last names, and 89% of full names (first, last format) while recovering 26% of the documents exactly." Deletion law cares about identifiers, so 89 percent of full names is the figure that bears here, not a reconstruction score. The better-known 92 percent exact-recovery headline is one table row and carries three conditions: GTR-base, an open-weights T5 retriever, at 32 tokens, with 50 correction steps plus sequence-level beam search. In the same table the same best configuration against a commercial embedding API, OpenAI's text-embedding-ada-002, gives 60.9 percent exact at 32 tokens and 8.0 percent exact at 128 tokens. A 2025 reproducibility study got 94.0 on the GTR 32-token row and 8.1 on the 128-token commercial row, and could not reproduce 60.9, because the 32-token inversion checkpoint was never released: "However, only the version with a maximum output length of 128 tokens, ada-ms-128, has been publicly released, while the ada-ms-32 model remains unavailable." Substituting the 128-token checkpoint gave 4.8. The result is real, and it depends on input length, on domain, and on having an inversion model trained for that exact embedder and length.

## The threat model, stated

This is not paste a vector and get the text back. It requires knowing which embedder produced the vector, query access to that embedder for every refinement step, and an inversion model trained per embedder; the GTR model was trained on 5M passages. Morris et al. state it directly: "Our threat model assumes that an adversary has black-box access to the model used to generate the embeddings in the compromised database." An embedding is not readable text. It is a vector that a purpose-trained, embedder-specific model can be steered toward.

The conclusion survives all of that intact, in the authors' words: "embeddings should be treated as highly sensitive private data and protected, technically and perhaps legally, in the same way as one would protect raw text."

## What a delete call does to an ANN index

Often less than it says. In hnswlib, the library under many HNSW implementations, `mark_deleted` "marks the element as deleted, so it will be omitted from search results" and `unmark_deleted` reverses it; a vector you can un-delete was never removed. Reclaiming the slot needs `allow_replace_deleted`, so a later insert can overwrite it. That is a property of that library's documented interface, so check three things in the index you run: whether delete tombstones or rewrites, what triggers compaction, and whether compaction rewrites the on-disk segment. Until it does, the vector is still on disk.

The easier leak sits next to the vector: most retrieval stores keep the source chunk in the payload for citation, and that is raw text, no inversion needed.

## Sources

- Morris, Kuleshov, Shmatikov, Rush. Text Embeddings Reveal (Almost) As Much As Text. EMNLP 2023, pages 12448-12460. DOI 10.18653/v1/2023.emnlp-main.765.
- Seputis, Li, Langerak, Mihailov. Rethinking the Privacy of Text Embeddings: A Reproducibility Study. RecSys 2025. DOI 10.1145/3705328.3748155, arXiv:2507.07700.
