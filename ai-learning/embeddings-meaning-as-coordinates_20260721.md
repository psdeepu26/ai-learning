# Embeddings: Turning Meaning into Coordinates

**Topic:** AI Learning  
**Date:** 21 July 2026  
**Focus:** Vector Search

## 🧒 Like you're 5

Imagine a giant toy room where every toy gets a spot. Cars sit near trucks, cats near dogs, and a toy airplane lands somewhere between vehicles and flying things. Nobody wrote labels on the floor—the room learned where each toy belongs from what it is like.

An embedding does this with meaning. It gives a word, sentence, image, or other item a list of numbers: its coordinates in a learned “meaning room.” Similar ideas usually land close together.

---

↓ **Now let’s call it what it really is** ↓

## 🔧 For the engineer

An embedding model maps input `x` to a dense vector `v ∈ ℝᵈ`. Geometry becomes a practical proxy for semantic relatedness.

- **Representation:** A sentence may become 384, 768, or 1,536 floating-point values. Dimensions are learned latent features, not human-readable labels.
- **Similarity:** Cosine similarity compares direction; dot product also reflects magnitude; Euclidean distance measures straight-line separation.
- **Retrieval:** Embed query and documents with the same model, then use approximate nearest-neighbor search to find top-`k` vectors.
- **Storage cost:** Raw float32 storage is roughly `N × d × 4 bytes`, before index overhead and metadata.
- **Model boundary:** Vectors from different models—or often different model versions—do not share a reliable coordinate system. Re-embed the corpus when switching.
- **Quality boundary:** Nearest does not mean correct. Domain mismatch, vague chunks, poor metadata, or unsuitable distance metrics can return convincing noise.

### Core calculation

`cosine(a, b) = (a · b) / (‖a‖ × ‖b‖)`

Result approaches `1` when vectors point in similar directions. Many embedding APIs return normalized vectors, making cosine similarity equivalent to dot product for ranking—but verify model documentation.

```python
# Tiny retrieval sketch
query_vector = embed("Why is checkout slow?")
results = vector_index.search(
    vector=query_vector,
    top_k=5,
    filter={"service": "checkout"}
)
```

> **Production rule:** Evaluate retrieval separately from generation. Track recall@k or nDCG on a labeled query set, inspect misses, and measure end-to-end latency. A fluent answer cannot prove retrieval worked.

## ⚡ Micro-action (5 minutes)

Pick three sentences: two that mean nearly the same thing and one unrelated sentence. Generate embeddings with one model, calculate all three pairwise cosine similarities, and predict the ranking before viewing results. Then change one sentence to include a shared keyword but different meaning—see whether semantics or lexical overlap wins.

---

**AI Learning · Embeddings & Retrieval**  
💫 Small steps. Every day.
