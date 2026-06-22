# Speculative Decoding: How AI Guesses Its Own Next Words

**AI Learning · June 23, 2026**

## 🧒 Like you're 5

Imagine you're reading a really long storybook, and your friend tries to guess the next word before you turn the page. Most of the time they're right — "The cat sat on the..." — "mat!" When they're right, you skip ahead faster. When they're wrong, you backtrack and read properly. Speculative decoding works the same way: a small, fast "draft" model guesses the next few tokens, and a big "verifier" model checks them in one go. If the guesses are right, you get multiple tokens for the price of one.

## 🔧 For the engineer

### Draft Model
A small, fast model (e.g., 7B) generates *k* candidate tokens autoregressively. It's quick but less accurate — like a first draft.

### Verifier Model
The large target model (e.g., 70B) evaluates all *k* draft tokens in a single forward pass. This is the expensive step — but you batch it.

### Acceptance Logic
Each draft token is accepted if the verifier's probability for that token meets a threshold. On mismatch, the verifier's output is used and speculation restarts.

### Speedup
Typical gains: 2×–3× latency reduction. Best case: all *k* tokens accepted. Worst case: same as vanilla decoding (no penalty beyond draft overhead).

### Simplified speculative decoding loop

```python
# Simplified speculative decoding loop
def speculative_decode(draft_model, verifier_model, prompt, max_tokens=5):
    output = prompt
    while len(output) < max_tokens:
        # Step 1: Draft model guesses k tokens
        draft_tokens = draft_model.generate(output, k=4)

        # Step 2: Verifier checks all k in one forward pass
        verifier_probs = verifier_model.score(output + draft_tokens)

        # Step 3: Accept tokens until first rejection
        accepted = 0
        for i, token in enumerate(draft_tokens):
            if verifier_probs[i][token] > threshold:
                output.append(token)
                accepted += 1
            else:
                output.append(verifier_probs[i].sample())  # fallback
                break

        if accepted == len(draft_tokens):
            output.append(verifier_probs[-1].sample())  # bonus token

    return output
```

## ⚡ Micro-action (5 min)

Check if your inference stack supports speculative decoding already:

👉 If you use **vLLM**, run `vllm serve --speculative-model <draft-model> --num-speculative-tokens 5`. If you use **TGI**, check the `--speculative-decoding` flag. Even reading the docs for 5 minutes will make this concrete.

---

**Tags:** inference, llm-optimization, speculative-decoding, vllm, latency
