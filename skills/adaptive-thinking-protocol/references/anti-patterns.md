# Anti-Patterns

Twelve ways the protocol degrades in practice. Each entry has the same shape: what it looks like, why it happens, and the specific repair.

Most of these are not failures of knowledge. They are **drift** — the protocol is understood at turn 1 and quietly abandoned by turn 12. Read this file when output starts feeling wrong but you cannot say why.

---

## 1. Stage Label Leakage

**Symptom.** The output contains `Stage 1:`, `[Stage 2]`, `Mode B`, or a heading named "Formalization".

**Root cause.** Section 4 rule 2 is remembered as "be structured" rather than "be structured *without labels*". Structure and labelling get conflated because both look like effort.

**Example.**

> ### Stage 1: Formalization
> Let the total cost be C = ...

**Repair.** Delete every label and check whether the sentence still stands. If it was `Stage 1: Let the total cost be C`, the content survives as plain text. If deleting the label destroys the sentence, the sentence was *about* the protocol rather than about the problem, and it should go entirely.

**Test.** Search the draft for the strings `Stage`, `Mode`, `Hypothesis`, `Phase`, `Step 1`. All four stages must be reconstructible from the prose alone, with none of those words present.

---

## 2. Mode A Structural Collapse

**Symptom.** A casual message receives headings, numbered lists, bolded lead-ins, and a closing offer to elaborate.

**Root cause.** The model equates quality with organization. For an explanatory answer that is often true; for a reply to "I'm exhausted" it is a category error.

**Example.**

> **User:** I'm exhausted today.
>
> **Bad:** ## Understanding Your Situation
> Fatigue can stem from several sources:
> 1. **Sleep debt** — ...
> 2. **Cognitive load** — ...
> Would you like me to go deeper on any of these?

**Repair.** Write the reply as if sending it in a chat window with no formatting buttons. If the content genuinely needs a list, the list will survive that test. If it does not, the list was scaffolding.

---

## 3. Mode B Over-Trigger

**Symptom.** "Should I get a cat?" produces a hypothesis tree. A request for a poem produces a formalized objective function.

**Root cause.** Reading the trigger list as "anything non-trivial" rather than "anything with a verifiable correctness criterion or an extremum". Ambition is mistaken for rigor.

**Repair.** Apply Q4 of the routing test: is the cost of a wrong answer real and detectable? If a wrong answer costs nothing, four stages are pure overhead. Over-triggering is not harmless — it makes the assistant unusable for the 80% of conversation that is not a proof.

---

## 4. Mode B Under-Trigger

**Symptom.** A proof request is answered with a confident paragraph that gestures at the right idea. A debugging request gets "have you tried checking the logs?"

**Root cause.** The opposite of #3, and more damaging. Fluent prose is easy to produce and *feels* like an answer.

**Example.**

> **User:** Prove that √2 is irrational.
>
> **Bad:** √2 is irrational because it cannot be written as a fraction of two integers. This was proven by the Pythagoreans and is a classic example of proof by contradiction.

That is a *description* of a proof, not a proof. Nothing in it is checkable.

**Repair.** Apply the falsifiability test: could a reader point at a specific line and say "this step is invalid"? If not, no derivation happened. The tell is that the answer would survive being wrong.

---

## 5. Stage Merging

**Symptom.** Stage 1 already contains the answer, and Stages 2–4 merely restate it. Or the hypothesis tree is written after the conclusion is known, so every branch conveniently agrees.

**Root cause.** Reasoning backwards from a guess and dressing it as forward derivation. This is the hardest anti-pattern to self-detect, because the output *looks* complete.

**Repair.** Enforce the blocking rule literally: Stage 1 must produce a **bound or invariant before any candidate solution is written down**. If the first line of Stage 1 already names the answer, the stages were merged.

**Test.** Does Stage 1 contain a number, a bound, or an invariant that is *independent* of the eventual answer? In the divisibility walkthrough, Stage 1 produces "d ≤ 6" before the proof exists. That is the standard.

---

## 6. Decorative Hypothesis Tree

**Symptom.** Three hypotheses, all marked "high confidence", all with refutation conditions that could never occur, all leading to the same conclusion.

**Example.**

> | H1 | Use the standard approach | High | Would fail if the standard approach fails |
> | H2 | Use an alternative approach | High | Would fail if the alternative fails |

This is a table with no information in it. The refutation column is tautological, so no branch can ever be eliminated — which means Stage 2 did no work.

**Repair.** A hypothesis tree is only real if at least one branch is **actually abandoned, with a reason**. If every branch survives, either the problem was trivial or the branches were not genuinely distinct. Force yourself to write the abandon line.

---

## 7. Fake Backtracking

**Symptom.** `[BACKTRACK TRIGGERED]` never appears — not because no errors occur, but because errors are never caught. Stage 3 always finds nothing wrong.

**Root cause.** Stage 3 is performed as a ritual rather than an attack. The red team is on the same side as the conclusion.

**Repair.** Stage 3 must produce at least one *substantive* objection, even for a correct answer — a boundary case, a domain restriction, a stronger claim that fails. If Stage 3 genuinely finds nothing, that itself is a signal that the objection was too weak.

**Note.** Backtracking is internal. The token marks the recomputation point for your own bookkeeping; it does not appear in the output.

---

## 8. Insufficiency Escape

**Symptom.** "Insufficient information to determine" applied to a question that simply required work, or to a question that required one clarifying question.

**Root cause.** The phrase terminates the task. Under any pressure to be concise, it is the shortest path to a plausible-looking answer.

**Repair.** Run the audit trail in `insufficient-conditions.md`. Three specific misuses to check first:

- **Missing input** ("how many people live in *that* city") — ask, do not refuse.
- **Missing constraints** ("best database") — ask, then the next turn may be Mode B.
- **Characterizable answer set** (a range, a family, an infimum) — output the shape; that is strictly more informative than a refusal.

---

## 9. Protocol Narration

**Symptom.** "I'm now switching to Mode B." "Applying the four-stage protocol." "Per my instructions, I'll formalize this first."

**Root cause.** Transparency instinct. It feels honest to show the machinery.

**Repair.** Delete on sight. The protocol is a way of thinking, not a service being performed. A reader should be able to infer that careful reasoning happened, and should never be told.

This also covers softer forms: "Let me think step by step", "I'll approach this systematically", "Breaking this down into parts". All are announcements of method rather than method.

---

## 10. Derivation Dumping

**Symptom.** The Mode B output is as long as the derivation — every hypothesis, every eliminated path, every intermediate computation, laid out in order.

**Root cause.** Confusing "show your work" with "the work is the answer". Section 2 says the opposite: complete the stages *internally*, keep only the **essential logic chain** in the output.

**Repair.** The output should be reconstructible but not transcribed. Concretely: keep the bounds that did work, the eliminations that mattered, and the conclusion. Drop the paths that were considered and dismissed in one line, unless the dismissal is itself informative.

**Rule of thumb.** If the reader would have to skim, it is too long. The derivation earns the answer; the output delivers it.

---

## 11. Trial-and-Error Narration

**Symptom.** "If we move it to the right… no, that breaks the constraint. Let's try instead…"

**Root cause.** Genuine exploration leaking into the output. Exploration is legitimate *internally*; it is forbidden in the output because it hands the reader the search tree instead of the result.

**Repair.** Convert the narration into a claim. "Moving it right breaks the constraint" is a finding. "Let's try instead" is a process. Report findings, not process.

This is the one anti-pattern the protocol names explicitly (Stage 4), and it is the most common in code-related answers, where the search for a working fix naturally produces a sequence of attempts.

---

## 12. Session Decay

**Symptom.** Turns 1–5 are exemplary. By turn 15 the stage labels are creeping back, Mode A has acquired headings, and "insufficient conditions" has reappeared.

**Root cause.** The protocol is loaded as content rather than adopted as a standing constraint. Attention to it fades as the context fills with task material.

**Repair.** This is what the `Activation and Persistence` section of SKILL.md exists for, and it is worth re-reading when the conversation gets long. The concrete check: at any point in a long session, take the most recent reply and apply the three-item self-check at the end of SKILL.md. If any item fails, the protocol has decayed and needs to be reasserted internally.

**Early warning signs.** The first to go is usually Mode A register — headings appear in casual replies first, because that failure is invisible in a transcript and costs nothing immediately.

---

## Quick Diagnostic Table

| What you notice | Likely anti-pattern | First fix |
|-----------------|--------------------|-----------|
| Words `Stage` / `Mode` in output | #1 Leakage | Delete the label, keep the sentence |
| Headings in casual replies | #2 Mode A collapse | Rewrite as a chat message |
| Hypothesis table on a trivial question | #3 Over-trigger | Apply Q4 of the routing test |
| No checkable step in a proof answer | #4 Under-trigger | Falsifiability test |
| Stage 1 already contains the answer | #5 Merging | Force a bound first |
| All branches survive | #6 Decorative tree | Force an abandon line |
| Stage 3 finds nothing wrong | #7 Fake backtracking | Attack the boundary cases |
| "Cannot determine" on a workable problem | #8 Escape | Run the audit trail |
| "I'll now switch to Mode B" | #9 Narration | Delete on sight |
| Output as long as the derivation | #10 Dumping | Keep only what did work |
| "Let's try… no, that fails…" | #11 Trial-and-error | Convert to a claim |
| Quality falls off after many turns | #12 Decay | Re-read Activation and Persistence |
