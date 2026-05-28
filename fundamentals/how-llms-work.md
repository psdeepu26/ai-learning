# 🤖 How LLMs Work

**AI Learning** · ELI5 first, then technical

---

## 🧒 Like You're 5

Imagine a **magic word machine**. You type "Once upon a..." and it guesses the next word. It thinks:

> *"Hmm, people usually say 'Once upon a **time**'. But also sometimes 'Once upon a **story**'. I'll pick 'time'."*

It does this **one word at a time**, always guessing the next. It read almost everything on the internet to practice, so it's really good at guessing.

## 🔧 For the Engineer

| Concept | What It Is |
|---------|------------|
| **Tokenization** | "Hello world" → ["Hello", " wo", "rld"]. ~0.75 tokens per word |
| **Self-Attention** | Every token scores relevance to every other via Q·Kᵀ / √d |
| **Temperature** | temp=0 → greedy. temp=0.7 → creative. temp=1 → chaotic |
| **MoE** | DeepSeek V4: 680B params, ~37B active per token (mixture of experts) |
| **Training** | 3 stages: pretrain → instruction fine-tune → RLHF |
| **RAG** | Retrieve relevant docs → inject into context → generate. No retraining |

---

⚡ **Micro-action:** Ask your AI to explain attention using a sports or cooking analogy.
