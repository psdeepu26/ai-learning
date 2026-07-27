# Temperature and Top-p: Steering the Next Token

**Topic:** AI Learning  
**Category:** Generation Controls  
**Date:** 28 July 2026

---

## 🧒 Like you're 5

Imagine choosing ice cream. One flavor is the clear favorite, but several others
look good.

**Temperature** decides how adventurous you feel. Low temperature means “pick
the favorite.” High temperature means “surprise me.” **Top-p** first removes the
unlikely flavors, then lets you choose only among a small group that together
covers most reasonable choices.

---

↓ Turn model scores into a controlled sampling policy ↓

## 🔧 For the engineer

An LLM outputs one **logit** per vocabulary token. Decoding transforms those raw
scores into a probability distribution, optionally filters candidates, then
selects the next token.

`P(token i) = softmax(logit_i / T)`

| Concept | Engineering meaning |
|---|---|
| **Temperature T** | `T < 1` sharpens probability gaps; `T > 1` flattens them. It changes relative probabilities across all candidates before sampling. |
| **Top-p nucleus** | Sort tokens by probability and retain the smallest set whose cumulative probability reaches `p`. Candidate count adapts to model confidence. |
| **Greedy decoding** | Select highest-probability token each step. Reproducible and cheap, but can become repetitive or lock into an early poor choice. |
| **Seed** | A fixed seed can make sampling repeatable only when model, runtime, hardware behavior, prompt, and decoding settings remain stable. |
| **Structured tasks** | For extraction, tool arguments, and config generation, favor low randomness plus schema-constrained decoding. Sampling settings alone do not guarantee valid JSON. |
| **Creative tasks** | Moderate temperature and top-p can increase variety. Measure useful diversity—not novelty alone—against task-specific evaluation criteria. |

```python
# Illustrative profiles; exact behavior varies by model/provider
factual = {"temperature": 0.1, "top_p": 0.9}
balanced = {"temperature": 0.7, "top_p": 0.9}
creative = {"temperature": 1.0, "top_p": 0.95}

# Change one control at a time during evaluation.
```

> **Common trap:** Temperature and top-p do not add knowledge or reasoning
> ability. They reshape token selection. If answers are wrong, first inspect
> prompt context, retrieval, model capability, and evaluation—not only
> randomness knobs.

---

## ⚡ Micro-action

Run one prompt three times with identical seed and `top_p=0.9`, using
temperatures `0.1`, `0.7`, and `1.0`. Compare factual consistency, wording
diversity, and format compliance. Pick settings from evidence, not folklore.

**AI Learning · Generation Controls**  
💫 Small steps. Every day.
