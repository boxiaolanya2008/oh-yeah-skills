# ai-plus-prompt

> English | [简体中文](README.md)

A **reasoning protocol engineering** project for LLMs, packaged as an installable Agent Skill. Its core is a dual-mode adaptive protocol: everyday conversation stays natural, while hard-constraint problems enter a four-stage reasoning pipeline automatically — **the switch is implicit, and the output carries no trace of the protocol**.

## Project Structure

```
ai-plus-prompt/
├── README.md                    # Chinese documentation (this repo's default)
├── README.en.md                 # English documentation (this file)
├── LICENSE                      # MIT License
└── skills/
    └── adaptive-thinking-protocol/
        ├── SKILL.md             # The protocol itself (English, ~100 lines)
        └── references/          # Example library (loaded on demand, ~1100 lines)
            ├── mode-routing.md          # Routing: 4-question test + 40 examples + 8 borderline cases
            ├── mode-a-playbook.md       # Mode A in practice: good-vs-bad pairs
            ├── mode-b-walkthroughs.md   # Four full four-stage derivations
            ├── insufficient-conditions.md # The three gates: 5 accepted, 6 rejected
            ├── anti-patterns.md         # 12 diagnosed failure modes
            └── domain-recipes.md        # Per-domain specialization across 5 domains
```

The protocol has exactly one source: `SKILL.md`. There is no parallel "Chinese original" file — a second copy is just a second place to drift.

## Core Design

### The Two Modes

| | Mode A: Regular Conversation (default) | Mode B: Extreme Reasoning |
|---|---|---|
| **Trigger** | Small talk, emotion, general knowledge, creative divergence, simple Q&A | Mathematical proofs, code debugging, logical deduction, constraint planning, multi-variable conflicts, self-referential paradoxes, and any hard constraint of the form "forbidden / must / optimize / prove" |
| **Execution** | Natural, fluent, direct; may ask follow-ups, joke, or admit ignorance | Four stages completed **internally**; only the essential logic chain survives into the output |
| **Output** | Stage labels, hypothesis tables, and formula dumps forbidden | Conclusion plus a constraint-coverage self-check; natural-language trial-and-error narration forbidden |

### The Four Stages (Mode B)

1. **Formalization and extremum probing** — convert to formulas / variables / state space; compute global invariants and bounds. **Before extremum probing is complete, constructing concrete instances is prohibited.**
2. **Hypothesis tree and minimal conflict set** — candidate paths H1/H2… each with confidence level and refutation conditions; explicitly declare abandoned paths and the reasons.
3. **Adversarial simulation and compliance review** — construct counterexamples for each conclusion; run literal + intent compliance review.
4. **Structured output** — conclusion plus a constraint-coverage self-check.

### The Three Gates: When "Insufficient Conditions" Is Allowed

All three must hold simultaneously:

1. Mode B has been entered;
2. In the Stage 2 hypothesis tree, the refutation conditions of every candidate path cannot be ruled out by the problem statement;
3. The explicit information and the standard axioms of the discipline have been exhausted, and there is still no unique solution.

Otherwise the phrase may never be used as a brush-off — in particular, never to avoid computation, avoid resolving an ambiguity, or exit early out of laziness.

## The Example Library

The protocol body is about 100 lines. The examples live in `references/` and load on demand. That library is where most of this project's substance sits.

| File | Lines | Contents |
|------|-------|----------|
| `mode-routing.md` | 153 | A four-question routing test (verifiability / extremum / derivation / cost of error), the explain-vs-produce distinction, 40 classified examples, and 8 borderline cases each adjudicated |
| `mode-a-playbook.md` | 173 | 8 good-vs-bad pairs (small talk, emotion, explanation, advice, creativity, simple fact, mixed turns, user-requested structure), a length calibration table, and a list of protocol residue to delete |
| `mode-b-walkthroughs.md` | 248 | Four problems run through all four stages: a divisibility proof, a Python late-binding debug, a bridge-crossing optimization, and a self-referential paradox. Each gives the internal derivation **and** the exact output — the length difference between them is the point |
| `insufficient-conditions.md` | 193 | 5 accepted cases (contradictory constraints / no minimum on an open interval / underdetermined system / one-degree-of-freedom geometry / integer polynomial family), 6 rejected cases (primality check / tech selection / stock prediction / ambiguous sentence / missing referent / Riemann Hypothesis), and an audit checklist |
| `anti-patterns.md` | 190 | 12 failure modes: label leakage, Mode A structural collapse, over-trigger, under-trigger, stage merging, decorative hypothesis trees, fake backtracking, insufficiency escape, protocol narration, derivation dumping, trial-and-error narration, session decay. Symptom / root cause / repair each, plus a diagnostic table |
| `domain-recipes.md` | 180 | Stage-by-stage specialization for coding, mathematics, data analysis, planning, and argument, with one full worked example per domain |

### Why the Examples Matter More Than the Rules

The rules can be memorized. The difference between "Stage 1 actually computed a bound" and "Stage 1 wrote a sentence that looks like formalization" cannot be memorized — it has to be seen. The library supplies **a completion standard you can check against**:

- Stage 1 must produce a bound **independent of the eventual answer** (in the divisibility proof, d ≤ 6 is derived before the proof exists);
- Stage 2 must contain at least one path that is **genuinely abandoned**;
- Stage 3 must find the objection that would **actually break** the conclusion, not a token self-audit;
- Stage 4's output must be **shorter** than the derivation.

## Quick Start

### Option 1: Install as a skill (recommended)

1. Copy the whole `skills/adaptive-thinking-protocol/` directory into `~/.workbuddy-ai/skills/`.
2. Refresh the skill list so the skill is picked up.
3. Just ask — once loaded, the protocol governs **every subsequent reply in the session**, with no need to restate it.

### Option 2: Extract as a system prompt

If the target environment has no skill support, the protocol body can be pasted directly:

1. Open `skills/adaptive-thinking-protocol/SKILL.md`.
2. Copy everything from `# Full-Scenario Adaptive Thinking Protocol V1.1.5` through the end of `## 4. Mandatory Discipline`.
3. Drop the YAML frontmatter and the `Reference Map` section — the first is skill metadata, and the second points at `references/` files that do not exist in a plain-prompt context.

Note that this form **excludes** the example library. It is the minimal viable version of the protocol, not the full package.

## Design Trade-offs

**Why does the protocol carry an `Activation and Persistence` section?**
A system prompt is always in force; a skill is **loaded on demand**. Without an explicit statement that it governs the whole session, runs silently, and is never explained to the user, the model may only follow the protocol for the turn that triggered it. That section introduces no new reasoning rules — it only fixes the scope.

**Why was the Chinese original `1.1.5.md` removed?**
Because it and `SKILL.md` were two copies of the same protocol. The cost of keeping both is not the extra bytes — it is the certainty of drift: one side gets edited and the other does not, and a reader has no way to tell which one is authoritative. Now there is a single source, and both READMEs describe rather than duplicate the protocol. The Chinese text remains available in git history.

**Why forbid stage labels while requiring the logic to stay traceable?**
Labels are for the writer, not the reader. The reader needs the logic chain, not a transcript of the derivation. Together the two rules produce answers that read like the result of careful thinking rather than a recording of it.

**Why three gates on "insufficient conditions"?**
Because the phrase terminates the task, which makes it the shortest exit under any pressure to be concise. And when the gate genuinely opens, the honest answer is usually not "I don't know" but a **precise characterization** — an empty set, an interval, a solution family, an infimum that is not attained. All five accepted cases in `insufficient-conditions.md` end with a structural conclusion; none ends with ignorance.

**Why was V1.1.0 removed?**
V1.1.0 was a single forced mode: every task ran the heavy pipeline and the full derivation was printed. It coupled reasoning depth to output format — depth could only be bought by sacrificing conversational quality. V1.1.5 decouples them: depth is guaranteed by Mode B, format is decided by the mode. One protocol means lower maintenance cost and wider coverage than two versions.

## Tags & Releases

| Tag | Artifact | Release Notes |
|-----|----------|---------------|
| [`v1.1.5`](../../releases/tag/v1.1.5) | `skills/adaptive-thinking-protocol/` | Dual-mode adaptive protocol with a three-gate "insufficient conditions" exit; ships with six reference files |

## License

This project is open source under the [MIT License](LICENSE).
