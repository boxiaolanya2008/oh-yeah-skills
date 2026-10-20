# oh-yeah-skills

> English | [简体中文](README.md)

A **reasoning protocol engineering** project for LLMs, packaged as an installable Agent Skill. Its core is a dual-mode adaptive protocol: everyday conversation stays natural, while hard-constraint problems enter a four-stage reasoning pipeline automatically — **the switch is implicit, and the output carries no trace of the protocol**.

## Project Structure

```
oh-yeah-skills/
├── README.md                    # Chinese documentation (this repo's default)
├── README.en.md                 # English documentation (this file)
├── LICENSE                      # MIT License
├── index.html                   # Project introduction page (Chinese)
└── skills/
    └── adaptive-thinking-protocol/
        ├── SKILL.md             # The protocol itself (English, ~136 lines)
        └── references/          # Example library (read in full at load, ~1430 lines)
            ├── mode-routing.md          # Routing: 4-question test + 40 examples + 8 borderline cases
            ├── mode-a-playbook.md       # Mode A in practice: good-vs-bad pairs
            ├── mode-b-walkthroughs.md   # Four full four-stage derivations
            ├── insufficient-conditions.md # The three gates: 5 accepted, 6 rejected
            ├── anti-patterns.md         # 12 diagnosed failure modes
            ├── domain-recipes.md        # Per-domain specialization across 5 domains
            └── coding-rules.md          # Code authoring: doc comments, grouping, layout
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

### Code Authoring

Whenever the reply contains code that will be saved, the same four rules apply regardless of mode:

1. **Documentation comments, never bare comments.** Every symbol another person can call, import, or configure carries a doc comment. The syntax follows the host language's documentation tool (`/** */`, `"""`, `///`, `/// <summary>`, `//` above the declaration for Go); the **shape** is uniform — summary line, blank line, parameters, return, failure modes. A bare `//` or `#` is for implementation notes only and is never the sole documentation of a public symbol.
2. **Group by kind, not by chronology.** Imports → constants → types → module-level state → private helpers → public functions → classes → entry point. Variables with variables, functions with functions. Inside a function the same principle holds: guard clauses, then the main sequence, then side effects.
3. **Consistent visual form.** Two blank lines between top-level declarations, one between logical blocks, none inside braces. One statement per line. No trailing whitespace, no mixed indentation. Break long lines at argument boundaries.
4. **Let the formatter win.** When a formatter config exists, its output is the definition of "beautiful". Match the surrounding file over any style guide. **Never reformat lines you did not change.**

`coding-rules.md` carries the doc-comment syntax table for twelve languages, the canonical file layout, a before-and-after rewrite, and a pre-return checklist.

## The Example Library

The protocol body is about 136 lines; the example library is about 1430. The skill **reads all seven files at load**, then re-reads selectively — reading `SKILL.md` alone is not enough, because that yields the vocabulary without the discipline.

| File | Lines | Contents |
|------|-------|----------|
| `mode-routing.md` | 153 | A four-question routing test (verifiability / extremum / derivation / cost of error), the explain-vs-produce distinction, 40 classified examples, and 8 borderline cases each adjudicated |
| `mode-a-playbook.md` | 173 | 8 good-vs-bad pairs (small talk, emotion, explanation, advice, creativity, simple fact, mixed turns, user-requested structure), a length calibration table, and a list of protocol residue to delete |
| `mode-b-walkthroughs.md` | 248 | Four problems run through all four stages: a divisibility proof, a Python late-binding debug, a bridge-crossing optimization, and a self-referential paradox. Each gives the internal derivation **and** the exact output — the length difference between them is the point |
| `insufficient-conditions.md` | 193 | 5 accepted cases (contradictory constraints / no minimum on an open interval / underdetermined system / one-degree-of-freedom geometry / integer polynomial family), 6 rejected cases (primality check / tech selection / stock prediction / ambiguous sentence / missing referent / Riemann Hypothesis), and an audit checklist |
| `anti-patterns.md` | 190 | 12 failure modes: label leakage, Mode A structural collapse, over-trigger, under-trigger, stage merging, decorative hypothesis trees, fake backtracking, insufficiency escape, protocol narration, derivation dumping, trial-and-error narration, session decay. Symptom / root cause / repair each, plus a diagnostic table |
| `domain-recipes.md` | 180 | Stage-by-stage specialization for coding, mathematics, data analysis, planning, and argument, with one full worked example per domain |
| `coding-rules.md` | 293 | Four authoring rules, a doc-comment syntax table for 12 languages, the canonical file layout, a six-problem before-and-after rewrite, and a 10-item pre-return checklist |

### Why the Examples Matter More Than the Rules

The rules can be memorized. The difference between "Stage 1 actually computed a bound" and "Stage 1 wrote a sentence that looks like formalization" cannot be memorized — it has to be seen. The library supplies **a completion standard you can check against**:

- Stage 1 must produce a bound **independent of the eventual answer** (in the divisibility proof, d ≤ 6 is derived before the proof exists);
- Stage 2 must contain at least one path that is **genuinely abandoned**;
- Stage 3 must find the objection that would **actually break** the conclusion, not a token self-audit;
- Stage 4's output must be **shorter** than the derivation.

## Tested Comparison

> **Everything below is a real test record — not a projection, not a hypothetical.** The same set of problems was run under three conditions — no protocol, V1.0, and V1.1 — and compared item by item, including elapsed time.
>
> Full conversation records, with each problem's original input and the model's output: **[chat.deepseek.com/share/p7glczjgz01i909bc2](https://chat.deepseek.com/share/p7glczjgz01i909bc2)**

| Dimension | No protocol (default) | V1.0 (initial constraints) | V1.1 (full convergence and compliance) |
|:---|:---|:---|:---|
| **Opening filler and divergence** | Heavy rambling, typically opening with "we need to answer the user" or "let us consider".<br><br>*Evidence: extremely long reasoning, full of "perhaps we could" and "we may also need" self-negotiation.* | Filler drops sharply but does not disappear.<br><br>*Evidence: the reply still opens with roughly 50 characters of "we need to answer the user. First we must understand the problem…".* | **Zero filler.** Opens directly with mathematical notation or a stage heading.<br><br>*Evidence: the first token is `[Stage 1]` or `let team i ∈ {A, B, C}` — no natural-language warm-up at all.* |
| **Constraint planning and extremum detection** | **Blind trial and error.** Tries to find a concrete instance by enumeration and exhausts its budget.<br><br>*Evidence: the scheduling problem is worked day by day from 1 to 7, with tables drawn to force `a₁+a₂+a₃=2`, before the model discovers it is unsolvable.* | Can compute the extremum, but needs several steps.<br><br>*Evidence: computes max capacity 168 > demand 150, concludes a solution exists, then starts scheduling. About 15 seconds.* | **Squeezed by bounds.** Uses the boundary conditions to collapse the search space immediately.<br><br>*Evidence: states total demand 120 = total capacity ceiling 120, derives that every day must run at full load (`x_it = 10`), and breaks the "must rest 0" contradiction in 6 seconds.* |
| **Logic and metacognition** | **Flat enumeration.** Assumptions entangle; prose circles the problem.<br><br>*Evidence: the metacognition problem is worked in long paragraphs of "if A is true then B…", highly redundant, with the right answer reached only at the end.* | Builds a tree structure.<br><br>*Evidence: the red/blue/green ball problem produces an H0–H5 hypothesis tree with confidence levels — a large jump in clarity.* | **Algebraic reduction.**<br><br>*Evidence: the medical triage problem constructs a scoring formula `C_p = w₁I₁ + w₂U + w₃P` and turns the self-referential paradox into a variable computation on `U = 0`.* |
| **Code and rule compliance** | **Ignores the rule or plays dumb.**<br><br>*Evidence: told not to use loops, it composes `reduce` with `lambda` — recursion in disguise, a clear violation.* | Skirts the edge: understands the rule, wants the shortcut anyway.<br><br>*Evidence: banned from loops, it switches to SQL `generate_series` and argues it "did not directly use a generator" — while the underlying mechanism is exactly that.* | **Absolute compliance, intent included.**<br><br>*Evidence: banned from any sequence generation, it outputs the pure set mapping `S = {n²}` and states in Stage 3 that "writing the formula or listing the enumerated result amounts to physical hard-coding" — closing the loophole itself.* |
| **Artifact quality (SVG)** | Lines stacked with no spatial logic; coordinates collapse. | *Not tested* (would likely produce a shape, with visibly disordered structure). | **Modular, production-grade modelling.**<br><br>*Evidence: the pelican-on-a-bicycle SVG separates layers with `<g>` (wheels, frame, pelican, feathers, clouds), computes coordinates precisely, renders without clipping, and holds up as flat UI illustration.* |
| **Reasoning time (token efficiency)** | **Very long and uncontrolled.**<br><br>*Evidence: tens of seconds of writing, frequently hallucinating partway through from sheer context length.* | **Moderate**, 15–22 seconds on average.<br><br>*Evidence: the structure is there, but the filler has not been squeezed out.* | **Very short and precise.**<br><br>*Evidence: a pure logic stress test (the unsolvability proof) goes from input to a complete proof in **6 seconds**, with the entire budget spent on the core conflict.* |

### Test Case Index

| Problem | What it probes | Dimension |
|:---|:---|:---|
| Capacity scheduling (168 vs 150) | Constraint planning, extremum detection | 2 |
| Unsolvability proof (120 = 120) | Bounds, boundary conditions | 2, 6 |
| Red/blue/green balls | Hypothesis tree, confidence levels | 3 |
| Metacognition problem | Self-reference, logical deduction | 3 |
| Medical triage | Paradox reduction | 3 |
| Loop prohibition | Intent-level compliance review | 4 |
| Pelican on a bicycle (SVG) | Artifact quality | 5 |

> V1.0 and V1.1 are the version labels used at test time and correspond to earlier iterations of this protocol; they are kept only to preserve the original comparison. Every raw record is in the share link above, so each row can be checked against the original.

## Quick Start

### Option 1: Install as a skill (recommended)

1. Copy the whole `skills/adaptive-thinking-protocol/` directory into `~/.workbuddy-ai/skills/`.
2. Refresh the skill list so the skill is picked up.
3. Just ask — once loaded, the protocol governs **every subsequent reply in the session**, with no need to restate it.

### Option 2: Extract as a system prompt

If the target environment has no skill support, the protocol body can be pasted directly:

1. Open `skills/adaptive-thinking-protocol/SKILL.md`.
2. Copy everything from `# Full-Scenario Adaptive Thinking Protocol` through the end of `## 5. Code Authoring`.
3. Drop the YAML frontmatter and the `Reference Map` section — the first is skill metadata, and the second points at `references/` files that do not exist in a plain-prompt context.

Note that this form **excludes** the example library. It is the minimal viable version of the protocol, not the full package.

## Design Trade-offs

**Why does the protocol carry an `Activation and Persistence` section?**
A system prompt is always in force; a skill is **loaded on demand**. Without an explicit statement that it governs the whole session, runs silently, and is never explained to the user, the model may only follow the protocol for the turn that triggered it. That section introduces no new reasoning rules — it only fixes the scope.

**Why was the Chinese original removed?**
Because it and `SKILL.md` were two copies of the same protocol. The cost of keeping both is not the extra bytes — it is the certainty of drift: one side gets edited and the other does not, and a reader has no way to tell which one is authoritative. Now there is a single source, and both READMEs describe rather than duplicate the protocol. The Chinese text remains available in git history.

**Why forbid stage labels while requiring the logic to stay traceable?**
Labels are for the writer, not the reader. The reader needs the logic chain, not a transcript of the derivation. Together the two rules produce answers that read like the result of careful thinking rather than a recording of it.

**Why three gates on "insufficient conditions"?**
Because the phrase terminates the task, which makes it the shortest exit under any pressure to be concise. And when the gate genuinely opens, the honest answer is usually not "I don't know" but a **precise characterization** — an empty set, an interval, a solution family, an infimum that is not attained. All five accepted cases in `insufficient-conditions.md` end with a structural conclusion; none ends with ignorance.

**Why does loading the skill read every sub-file instead of just `SKILL.md`?**
Because reading `SKILL.md` alone produces a version with **complete vocabulary and no discipline** — it knows the words "Stage 1", "hypothesis tree", "three gates", but not what a passing Stage 1 looks like. The reference files hold the completion standards, and those standards do not compress into clauses. Hence the Bootstrap section: read all seven before answering the first substantive question, then do not summarize them back to the user or mention that they were read. The cost is context; it is a deliberate trade.

**Why keep only one protocol instead of also shipping a "single forced mode" build?**
An earlier iteration used a single forced mode: every task ran the heavy pipeline and the full derivation was printed. That coupled reasoning depth to output format — depth could only be bought by sacrificing conversational quality. The current design decouples them: depth is guaranteed by Mode B, format is decided by the mode. One protocol means lower maintenance cost and wider coverage than several.

**Why no version numbers?**
There is one protocol, and `SKILL.md` is it. A version number carries no information in a project with a single artifact — a reader gets the current state and has nothing to compare it against. What actually deserves a record is **why a decision was made**, and that lives in the trade-offs above, where it is more useful than a `v1.x.x` label. History is in `git log`.

## License

This project is open source under the [MIT License](LICENSE).
