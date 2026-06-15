# Knowledge Distillation: Teaching Small Models to Think Like Giants

**Date:** June 16, 2026  
**Topic:** AI Learning  
**Category:** inference-optimization

## 🧒 Like You're 5

Imagine your school teacher is really, really good at math — she can solve any problem. But she's busy and can't be in every classroom at once.

So instead of trying to copy everything she *knows*, the school asks: **"Can we train a smaller, faster tutor to give the same *feel* for what's right — not just the answer, but the teacher's confidence?"**

That smaller tutor watches how the teacher *thinks* — including her "I'm 70% sure it's B" probabilities — and learns to mimic that reasoning. She doesn't know everything the teacher knows, but on the questions kids actually ask, she gives really similar answers. That's knowledge distillation.

## 🔧 For the Engineer

### The Core Problem

Large models (teachers) like GPT-4 or a 70B Llama are incredibly accurate but expensive to serve — high VRAM, slow inference, cost per token adds up fast. You want the accuracy of a giant model but the speed and cost of a tiny one in production.

### How KD Works

Instead of training a small model (student) on hard labels ("the answer is B"), you train it on the teacher's **soft probability distribution** across all classes. The teacher's output contains dark knowledge — the relative probabilities between wrong answers — that encodes rich information about how concepts relate.

**Standard Training (Hard Labels):**
```python
# Label = one-hot
loss = CrossEntropy(logits, target)
# [0, 0, 1, 0, 0] → "it's class 2"
```

**Knowledge Distillation (Soft Targets):**
```python
# Teacher outputs soft probs
teacher_probs = softmax(teacher_logits / T)
student_probs = softmax(student_logits / T)

# T = temperature (higher = softer)
loss = KL_divergence(teacher_probs, student_probs) * T²
# + alpha * CrossEntropy(student, hard_label)
```

### The Temperature Parameter (T)

Raising temperature **T** spreads out the probability distribution, making the teacher's "dark knowledge" more visible. Without high T, the distribution is too sharp and you lose the relational signal between classes. Typically T=3 to T=20.

| T = 1 (Normal Softmax) | T = 5 (Distillation Temperature) |
|---|---|
| `[0.001, 0.001, 0.997, 0.0005, 0.0005]` → Nearly one-hot | `[0.02, 0.05, 0.71, 0.15, 0.07]` → Class 3 & 4 are close to correct |

### Variants You'll Actually Use

- **Response-Based KD** — Output layer only. Simple, works with any teacher.
- **Feature-Based KD** — Match intermediate hidden states. More powerful but requires access to teacher's internals.
- **Self-Distillation** — Teacher = Student (same architecture, different training stages).
- **Dataset Distillation** — Compress entire training set into a small synthetic dataset.

### Real-World Impact

- **DistilBERT** — 60% faster, 40% smaller, retains 97% of BERT's GLUE score
- **TinyBERT** — 7.5x smaller, 9.4x faster on BERT-base
- **NVCF relevance** — Distilled models reduce inference cost per request, directly impacting serving infrastructure throughput and cost

### KD vs Quantization vs Pruning

| Technique | What | When |
|---|---|---|
| KD | Train small model to act big | Architecture change |
| Quantization | Reduce weight precision (FP32→INT8) | Deployment optimization |
| Pruning | Remove redundant weights/heads | Post-training compression |

**Best results: Distill → then quantize → then prune.** Each step compounds the compression.

## ⚡ Micro-Action

Run the Python script in this card's HTML version to perform a minimal knowledge distillation experiment with HuggingFace `transformers`. Train a DistilBERT student on SST-2 using BERT teacher's soft outputs, then compare accuracy vs latency. The student should be ~2x faster with minimal accuracy drop.
