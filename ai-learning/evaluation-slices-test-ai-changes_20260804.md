# Evaluation Slices: Test AI Changes on the Cases That Matter

**Topic:** AI Learning  
**Category:** Evaluation  
**Date:** 04 Aug 2026

## 🧒 Like you're 5

Imagine tasting a new recipe. If you only taste one spoonful, you might think the whole pot is perfect—even though the salty part is hiding at the bottom. An evaluation slice is a small, named group of examples that represents one important flavor: easy questions, tricky edge cases, long inputs, or safety-sensitive requests.

When you change the recipe, taste every important slice—not just a random spoonful.

↓ Now call the tasting groups evaluation slices ↓

## 🔧 For the engineer

An evaluation slice is a stable subset of test cases selected by a meaningful property. It makes model or prompt changes diagnosable: an aggregate score can stay flat while one high-risk slice quietly regresses.

- **1 · Name the risk:** Group cases by what can fail: multilingual input, long context, tool calls, refusals, latency, or high-value workflows.
- **2 · Freeze the set:** Version examples and expected behavior. Do not let the benchmark drift every time a result looks inconvenient.
- **3 · Track slice metrics:** Measure task quality plus p95 latency, token cost, invalid-tool-call rate, and refusal precision.
- **4 · Gate selectively:** Use thresholds per slice. A global average must not hide a failure in a safety or revenue-critical slice.

### Minimal case format

```json
{
  "id": "tool-call-017",
  "slice": ["tool-calls", "edge-cases"],
  "input": "...",
  "expected": {"tool": "lookup", "args_valid": true},
  "threshold": {"pass_rate": 0.98}
}
```

### Change decision

- **Ship:** Overall score improves, no critical slice breaches its threshold, and cost/latency stay within budget.
- **Investigate:** The average improves but a slice regresses. This is often a routing, prompt, retrieval, or data-distribution clue—not noise to average away.

**Key idea:** evaluation is not one number. It is a set of lenses aligned to the ways the system can hurt or help users.

## ⚡ Micro-action

Take one existing AI test set and add three labels for failure risk—for example `long-input`, `tool-call`, and `fallback`. Compute pass rate for each label separately. Pick the slice with the lowest rate and add one representative case to tomorrow's regression gate.

_AI Learning · 💫 Small steps. Every day._
