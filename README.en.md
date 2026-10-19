# ai-plus-prompt

> English | [简体中文](README.md)

A **Chinese-language reasoning prompt engineering** toolkit for LLMs. It ships two versions — from a "single forced reasoning protocol" to a "full-scenario adaptive thinking protocol" — ready to paste into any model's system prompt.

## Project Structure

```
ai-plus-prompt/
├── README.md        # Chinese documentation (this repo's default)
├── README.en.md     # English documentation (this file)
├── LICENSE          # MIT License
├── 1.1.0.md         # Protocol V1.1.0: single forced-reasoning protocol
└── 1.1.5.md         # Protocol V1.1.5: full-scenario adaptive protocol (current recommendation)
```

## Version Differences

| Aspect | V1.1.0 (Single Forced Reasoning) | V1.1.5 (Full-Scenario Adaptive) |
|--------|----------------------------------|--------------------------------|
| Modes | Single mode — every task goes through the heavy reasoning pipeline | Dual mode: Mode A (regular chat) / Mode B (extreme reasoning), switched implicitly by intent |
| Thinking Output | Must start with `[阶段一]`; full derivation chain is printed | Four stages completed **internally**; only the essential logic chain is kept in the output |
| Output Style | Forced headings + formulas + short sentences | Mode A: natural conversation; Mode B: structured and concise |
| Stage Labels | Explicitly printed | **Forbidden** — output must contain no stage labels, though logic must remain traceable to each stage |
| Backtracking | Prints the `[回溯触发]` token on errors | Marks the token and recomputes from the nearest correct node, without exposing it externally |
| "Insufficient Data" Exit | Requires a proof of unsolvability in Stage 2 | Requires all three gate conditions to hold simultaneously, preventing lazy cop-outs |
| Length | 26 lines, compact | 66 lines, sectioned and structured |

Both versions share the **core reasoning skeleton** (used by V1.1.0 and Mode B of V1.1.5):

1. **Stage 1**: Formalization & extremum probing — convert to formulas/variables, compute global invariants and bounds.
2. **Stage 2**: Hypothesis tree & minimal conflict set — candidate paths H1/H2… each with confidence level and refutation conditions.
3. **Stage 3**: Adversarial simulation & compliance review — red-team the conclusions, run literal + intent compliance double review.
4. **Stage 4**: Structured output & constraint-coverage self-check.

## Which Version Is Better (By Scenario)

| Scenario | Recommended | Why |
|----------|-------------|-----|
| Everyday chat, emotional support, factual Q&A | ✅ **V1.1.5** | Mode A replies naturally — no forced hypothesis tables or formula dumps |
| Creative writing, brainstorming | ✅ **V1.1.5** | Mode A keeps creative freedom; it can follow up and joke |
| Mathematical proofs, code debugging | 🟡 Either | Both run the full four stages; V1.1.5 is more concise, V1.1.0 more transparent |
| Competition problems, hard-constraint logic puzzles | 🟡 Either | Choose V1.1.0 for a visible step-by-step trace; V1.1.5 for answer quality |
| Auditing the full chain of thought (prompt research / debugging) | ✅ **V1.1.0** | Forces full derivation output, easy to inspect stage by stage |
| Long-term general-purpose system prompt | ✅ **V1.1.5** | Dual-mode adaptation balances conversation quality and reasoning depth — the widest coverage |
| Preventing the model from hiding behind "insufficient data" | ✅ **V1.1.5** | The triple gate condition significantly tightens the "cannot determine" exit |

**Summary**:

- **Pick V1.1.5 for general use** — it is the only version that "chats when it should chat and reasons hard when it must", making it the best default system prompt.
- **Pick V1.1.0 for pure reasoning stress-tests / process inspection** — it forces every step into the open, ideal for evaluating model reasoning or studying prompt design.

## Quick Start

1. Copy the entire content of the target version file (e.g. `1.1.5.md`).
2. Paste it into the model's system prompt (or the first user message).
3. Just ask — V1.1.5 judges intent and switches modes automatically, no manual declaration needed.

## Tags & Releases

| Tag | File | Release Notes |
|-----|------|---------------|
| [`v1.1.0`](../../releases/tag/v1.1.0) | `1.1.0.md` | Initial release: single forced four-stage reasoning protocol |
| [`v1.1.5`](../../releases/tag/v1.1.5) | `1.1.5.md` | Upgraded to dual-mode adaptive protocol; tightened the "insufficient data" exit |

## License

This project is open source under the [MIT License](LICENSE).
