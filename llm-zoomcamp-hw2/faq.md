# FAQ: Vector Search Homework (LLM Zoomcamp)

This document answers common questions about the concepts and implementation covered in `vector_hw.ipynb`.

---


**Q: What is the `Embedder` class and what does it do?**

`Embedder` encodes plain text into fixed-size numerical vectors (embeddings). You call `embed.encode(text)` for a single string or `embed.encode_batch(list_of_texts)` for a list. The resulting vectors capture the semantic meaning of the text and can be compared mathematically.

---

**Q: Why use dot product instead of Euclidean distance?**

For normalized embeddings, the dot product is equivalent to cosine similarity and is computationally cheaper. It is the standard for semantic search with sentence-transformer-style models.

---

**Q: What is chunking and why is it needed?**

Chunking splits large documents into smaller overlapping pieces so that:
- Each chunk fits within the embedding model's token limit
- Relevant context is not cut off at boundaries (overlap helps)

```python
chunks = chunk_documents(documents, size=2000, step=1000)
```

`size=2000` means each chunk is ~2000 characters; `step=1000` means consecutive chunks overlap by 1000 characters.

---

**Q: What does `VectorSearch` do differently from the manual dot product approach?**

`VectorSearch` wraps the same idea in a reusable class. You fit it once with `vindex.fit(X, chunks)` and then query with `vindex.search(q_embed, num_results=5)`. It returns the top-N most relevant chunks rather than just the single best match.

---

**Q: Why use hybrid search instead of just vector or keyword search alone?**

Each method has blind spots:
- **Vector search** captures semantic meaning but can miss exact keyword matches
- **Keyword search** is precise on exact terms but fails on paraphrase or synonyms

Combining both with RRF generally yields more robust and accurate results.

---

**Q: `embed.encode()` returns an error about input length — what's wrong?**

The embedding model has a token limit (commonly 512 tokens). If you pass a very long string, it gets truncated or errors. Use `chunk_documents` to split content before encoding.

---

**Q: `X.dot(q1_embed)` gives a shape error — why?**

Ensure `q1_embed` is a 1D array of shape `(embedding_dim,)` and `X` is 2D of shape `(num_chunks, embedding_dim)`. If `embed.encode()` returns a 2D batch output, flatten or index with `[0]`.

---