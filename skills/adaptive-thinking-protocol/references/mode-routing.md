# Mode Routing

Routing is the only decision the protocol makes on **every single turn**. Getting it wrong in either direction breaks the protocol more visibly than any other mistake: a false Mode B turns casual chat into a form, and a false Mode A turns a proof into vibes.

## The Four-Question Test

Run these in order. Stop as soon as an answer settles it.

**Q1 — Is there a verifiable correctness criterion?**
Can the answer be checked and be *wrong*? A proof, code that must run, a constraint that must hold, a count that must be exact, a clause that must be present. If yes → lean Mode B.

**Q2 — Is there an extremum or optimization?**
Words like *best, minimum, shortest, optimal, maximize, as few as possible*. If the request asks for an extremum under stated constraints → Mode B. If it asks for "a good option" with no constraints → Mode A.

**Q3 — Must the answer be derived rather than retrieved or generated?**
Derivation from premises, case analysis, contradiction, invariants → Mode B. Retrieval of known facts, explanation of known mechanisms, opinion, invention → Mode A.

**Q4 — Is the cost of a wrong answer real and detectable?**
Money, safety, a broken build, a failed exam, an incorrect medical or legal claim. Q4 is the tie-breaker: it upgrades a borderline Q1–Q3 *maybe* into a definite Mode B, and it never downgrades a definite yes.

Decision rule:

- **Any of Q1–Q3 is a clear yes, and Q4 is yes** → Mode B.
- **Q1–Q3 all no** → Mode A.
- **Q1–Q3 borderline and Q4 is no** → Mode A. Do not spend four stages on a question whose wrong answer costs nothing.

### The explain/produce distinction

This single distinction resolves most of the hard calls:

| Request shape | Mode | Why |
|---------------|------|-----|
| "Explain why X happens" | A | Explaining a known mechanism is retrieval |
| "Produce X that works" | B | Producing a working artifact is verification |
| "Explain why my code crashes" | A | The fact is already known and fixed |
| "Find why my code crashes" | B | The cause is unknown and must be derived |

The word *why* alone does not trigger Mode B. The word *find*, *prove*, *fix*, *verify*, or *guarantee* does.

## 40 Classified Examples

### Mode A — Regular Conversation

| # | User input | Category |
|---|-----------|----------|
| 1 | "Hey, how's it going?" | Small talk |
| 2 | "I'm so done with this week." | Emotional exchange |
| 3 | "Tell me a joke about databases." | Creative divergence |
| 4 | "What's the capital of Australia?" | Simple factual |
| 5 | "What's 17 × 24?" | Simple factual (single-step arithmetic is retrieval, not derivation) |
| 6 | "Explain how TCP/IP works." | General knowledge explanation |
| 7 | "What does 'idempotent' mean?" | Definition |
| 8 | "Explain why 0.1 + 0.2 is 0.30000000000000004 in JavaScript." | Explanation of a known mechanism |
| 9 | "Summarize this article in three sentences." | Summarization |
| 10 | "Write a haiku about a failing test suite." | Creative divergence |
| 11 | "Name my coffee shop." | Creative divergence |
| 12 | "Should I take the job offer?" | Advice without stated constraints |
| 13 | "Is it normal to feel lost at 30?" | Emotional exchange |
| 14 | "What's the difference between HTTP/1.1 and HTTP/2?" | General knowledge explanation |
| 15 | "Give me some ideas for a team offsite." | Creative divergence |
| 16 | "Who wrote 'The Left Hand of Darkness'?" | Simple factual |
| 17 | "How do I get better at chess?" | General advice |
| 18 | "Explain recursion like I'm five." | General knowledge explanation |
| 19 | "Translate this sentence into French." | Transformation, no correctness derivation |
| 20 | "What should I cook with leftover rice?" | Everyday advice |
| 21 | "Help me brainstorm names for a podcast." | Creative divergence |
| 22 | "Why do we yawn?" | General knowledge explanation |
| 23 | "Recommend a book about systems thinking." | Recommendation |
| 24 | "My teammate keeps interrupting me in meetings." | Emotional exchange |
| 25 | "Explain the plot of Inception." | Retrieval |

### Mode B — Extreme Reasoning

| # | User input | Trigger |
|---|-----------|---------|
| 26 | "Prove that √2 is irrational." | Mathematical proof |
| 27 | "Prove that n³ − n is divisible by 6 for every integer n." | Mathematical proof |
| 28 | "This function returns 0.30000000000000004 instead of 0.3 — fix it." | Code fix with a verifiable criterion |
| 29 | "Why does this loop print 3 three times?" (with code) | Debugging: cause unknown |
| 30 | "Find the bug in this parser." | Debugging |
| 31 | "Write a regex that matches ISO-8601 dates and nothing else." | Verifiable correctness |
| 32 | "Four people must cross a bridge at night with one torch; minimize total time." | Optimization under constraints |
| 33 | "Schedule these 12 tasks on 3 machines so that no task is late." | Constraint planning |
| 34 | "Design a schema that guarantees no double-booking." | Constraint satisfaction |
| 35 | "Is this SQL query equivalent to that one?" | Equivalence proof |
| 36 | "What's the minimum number of coins for 1.37 using 1/5/10/25?" | Extremum |
| 37 | "This statement is false. Is it true?" | Self-referential paradox |
| 38 | "Three people each claim the other two are lying; who is telling the truth?" | Logical deduction |
| 39 | "Find the flaw in this proof." | Logical deduction |
| 40 | "Given these five constraints, is a valid configuration even possible?" | Feasibility proof |

## Eight Borderline Cases

Each of these is genuinely arguable. The resolution matters more than the label.

### 1. "Explain why 0.1 + 0.2 !== 0.3 in JS"

**Mode A.** The mechanism is known; the request is retrieval and explanation. The four stages would add nothing and the output would read like a lecture.

*But*: if the same user then says "now fix my totals", the next turn is Mode B — that turn has a verifiable correctness criterion.

### 2. "Is my code correct?"

**Mode B.** Correctness is a claim that can be false. Verification requires enumerating what could go wrong, which is exactly the Stage 2 hypothesis tree.

### 3. "Should I rewrite this service in Rust?"

**Mode A** by default — it is advice, and there is no stated objective function.

**Mode B** if the user supplies the objective: "cut p99 latency below 50 ms without raising infra cost above $2k/month". Now there is an extremum under constraints.

The lesson: **the same question flips modes when constraints appear.** Constraints are the mode switch.

### 4. "What's the best sorting algorithm?"

**Mode A** in the abstract. There is no best; the honest answer is a short tour of trade-offs.

**Mode B** when scoped: "10M rows, 200 MB RAM, 8 cores, need stable sort, minimize wall time." Now the extremum is well-defined and the answer is derivable.

### 5. "Write a regex for email addresses"

**Mode B.** A regex either matches the intended set or it does not. There is a correctness criterion, and a subtly wrong answer is worse than no answer because it looks right.

### 6. "Summarize this contract" vs "audit this contract for risky clauses"

**Summarize → Mode A.** Compression of given text.

**Audit → Mode B.** Omission is a correctness failure: a clause that exists and was not flagged is a wrong answer. That asymmetry (missing something is an error) is what forces the hypothesis tree — you have to enumerate the clause categories that could be present.

### 7. "Plan a three-day trip to Kyoto"

**Mode A.** No hard constraints; any reasonable itinerary satisfies the request.

**Mode B** once constraints bind: "hit all seven temples, under two hours of transit per day, wheelchair accessible, no temple visited twice." Now feasibility and optimality are real questions.

### 8. "Prove it"

A bare follow-up. **Mode B** — the previous Mode A answer made a claim, and the user is now asking for the derivation. Never answer "prove it" with more explanation of the same claim; that is a mode error disguised as a style choice.

## Mixed Turns

Real messages often contain both. Split the message and route each part:

> "My deploy keeps timing out at 30s — also, any idea what I should have for dinner?"

- Part 1 → Mode B: cause unknown, verifiable, costly.
- Part 2 → Mode A: no correctness criterion.

Handle them in one reply: run the stages internally for part 1, answer part 2 in plain language, and let the two registers coexist. Do not wrap the dinner suggestion in a hypothesis table, and do not answer the timeout with "have you considered checking your logs?"

## A Note on Under-Triggering

The more common failure in practice is **under-triggering**: answering a proof request with a confident paragraph that gestures at the right idea without deriving it. The tell is that the answer would survive being wrong — nothing in it is checkable. If a reader could not point at a specific line and say "this step is invalid", Mode B was skipped.
