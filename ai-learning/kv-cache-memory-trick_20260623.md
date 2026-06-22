# KV Cache: The Secret Memory Trick That Makes LLMs Fast

**Date:** June 23, 2026 · **Topic:** AI Learning · **Category:** inference-optimization

---

## 🧒 ELI5

Imagine you're writing a story, one word at a time. Every time you add a new word, you re-read the entire story from the beginning to remember what you've already written. That would take *forever*.

A KV Cache is like keeping sticky notes summarizing everything you've written so far. When you add a new word, you just glance at the sticky notes instead of re-reading the whole story. It's the reason an LLM can generate 10,000 words without getting exponentially slower.

↓

## 🔧 Technical Deep-Dive

### What KV Cache actually stores

During autoregressive decoding, each token attends to all previous tokens via multi-head attention. The **Key (K)** and **Value (V)** projections for each layer are computed at generation time and cached. The **Query (Q)** is only computed for the current token.

```python
# Pseudo-compute for decoding step t
for layer in range(num_layers):
    q = compute_q(hidden_states[t])            # only current token
    k_cache, v_cache = kv_caches[layer]         # all previous tokens
    attn = softmax(q @ k_cache.T / sqrt(dim))
    out = attn @ v_cache
    # Append current KV to cache
    kv_caches[layer].append(compute_kv(hidden_states[t]))
```

### Memory cost: the bottleneck

```
KV_cache_size = 2 × L × H × S × D_h × bytes_per_element
# Llama 70B, L=80, H=64, S=32768, D_h=128, fp16
# ≈ 8.9 GB just for KV cache per request!
```

### Multi-Query & Grouped-Query Attention (MQA / GQA)

| Variant | Q heads | K/V heads | Cache reduction |
|---------|---------|-----------|-----------------|
| MHA     | 32      | 32        | 1× (baseline)   |
| MQA     | 32      | 1         | 32×             |
| GQA     | 32      | 4         | 8×              |

### Production implications (SRE perspective)

- **vLLM / TensorRT-LLM** — paged KV cache (like virtual memory) to reduce fragmentation waste
- **Prefix caching** — share KV cache across requests with common prompt prefixes (huge for system prompts / RAG)
- **CPU offloading** — SGLang can spill KV cache to CPU for very long contexts
- **GPU memory monitoring** = your best proxy for KV cache pressure

## ⚡ Micro-action (5 min)

```bash
python3 -c "
L, H, D_h, dtype_bytes = 80, 64, 128, 2
for ctx_len in [2048, 4096, 8192, 16384]:
    size_gb = (2 * L * H * ctx_len * D_h * dtype_bytes) / (1024**3)
    print(f'  ctx={ctx_len:>6} → KV cache ≈ {size_gb:.2f} GB')
"
```

Compare to your GPU memory. If a single 16K-context request uses 4+ GB just for KV cache on a 40 GB A100, you know exactly why batch size drops at long contexts.

---

*Tags: LLM Inference, KV Cache, GPU Memory, vLLM, Autoregressive Generation*
