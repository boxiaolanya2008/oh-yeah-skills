# Mode B Walkthroughs

Four problems, each run end to end. For every one, the internal derivation is shown in full and then the **exact output** is given separately — because the gap between those two is the whole point of the protocol. The derivation is long. The output is short. Nothing in the output is labelled.

Read these for shape, not content. The specific mathematics will not transfer; the discipline of "probe the bounds before you build an example" will.

---

## Walkthrough 1 — A divisibility proof

**Problem:** Prove that n³ − n is divisible by 6 for every integer n.

### Internal derivation

**Stage 1 — Formalization and extremum probing**

- Factor: n³ − n = n(n−1)(n+1), the product of three consecutive integers.
- Target divisor 6 = 2 · 3, with 2 and 3 coprime, so it suffices to establish 2 | · and 3 | · separately.
- Extremum probe: what is the *largest* constant d dividing n³−n for all n? Any such d must divide the values at n = 0, 1, 2, 3, which are 0, 0, 6, 24. Hence d | gcd(6, 24) = 6, so d ≤ 6. Since 6 also works, the bound is exact.
- Consequence for the write-up: the claim is **tight**, not merely true. Saying so is part of answering well, and it is the kind of statement that cannot be produced without having done the probe.

**Stage 2 — Hypothesis tree and minimal conflict set**

| Path | Content | Confidence | Refutation condition |
|------|---------|-----------|---------------------|
| H1 | Factor into three consecutive integers; use 2 \| · and 3 \| · separately | High | Fails if the factors are not in fact consecutive — they are: n−1, n, n+1 |
| H2 | Induction on n, using (n+1)³ − (n+1) − (n³ − n) = 3n(n+1) | High | Fails if 3n(n+1) is not always divisible by 6; it is, since n(n+1) is even. Valid but longer |
| H3 | Enumerate n mod 6, six cases | High | Not refutable, but adds no insight and multiplies the opportunities for a case error |

Chosen: H1 as the proof, H2 as a silent cross-check. **Abandoned: H3** — correct but redundant, and a six-case proof is six places to slip.

**Stage 3 — Adversarial simulation**

- Negative integers: n = −2 gives −8 + 2 = −6. The consecutive-product argument never uses sign, so it covers ℤ rather than ℕ. This is the most common gap in textbook versions.
- Zero and one: both give 0, and 6 | 0. No special case needed.
- Stronger claim check: is it divisible by 12? n = 2 gives 6, so no. The tightness result from Stage 1 already predicted this.
- Ambiguity: "divisible" presupposes integers. If the domain were reals the claim is meaningless, so the domain is ℤ.

**Stage 4 — Structured output**

### Output

> n³ − n factors as n(n−1)(n+1) — the product of three consecutive integers.
>
> **Divisibility by 2.** Among any three consecutive integers at least one is even, so the product is even.
>
> **Divisibility by 3.** Every integer is congruent to 0, 1, or 2 mod 3. If n ≡ 0 then 3 | n; if n ≡ 1 then n−1 ≡ 0, so 3 | (n−1); if n ≡ 2 then n+1 ≡ 0, so 3 | (n+1). In every case one factor is divisible by 3.
>
> Because 2 and 3 are coprime and both divide the product, 6 divides it.
>
> The argument never uses the sign of n, so it covers all integers including 0 and negatives.
>
> **The bound is tight.** At n = 2 the value is 6, so no constant larger than 6 divides n³ − n for every n. The claim cannot be strengthened to 12 or 24.

### What to copy

The extremum probe did real work twice: it established tightness, and it *predicted* the refutation of the "divisible by 12" variant before anyone asked. Bounds are not decoration.

---

## Walkthrough 2 — A debugging problem

**Problem:**

```python
funcs = []
for i in range(3):
    funcs.append(lambda: i)
for f in funcs:
    print(f())
```

This prints `3`, `3`, `3`. Why?

### Internal derivation

**Stage 1 — Formalization and extremum probing**

- Model: a closure captures a *cell*, not a value. The question is whether the three lambdas hold three distinct cells or one shared cell.
- State space: {binding strategy: by-value, by-reference} × {3 closures}.
- Extremum probe on the observable: if capture were by-value, the printed values would be a subset of the loop's range, so each output would lie in {0, 1, 2}. The observed value 3 lies **outside** that range. That single bound already proves the value was read after the loop ended, before any theory is invoked.

**Stage 2 — Hypothesis tree and minimal conflict set**

| Path | Content | Confidence | Refutation condition |
|------|---------|-----------|---------------------|
| H1 | Late binding: all three closures share the cell for `i`, which ends at 3 | High | Refuted if any closure printed a different value, or if the output were 0, 1, 2 |
| H2 | Print buffering or ordering artifact | Low | Refuted: buffering reorders output, it cannot change values, and three distinct cells cannot collapse into one value |
| H3 | Integer interning or caching | Low | Refuted: caching affects object identity, not the integer's value |
| H4 | `range(3)` yielding four values | Low | Refuted: then there would be four closures, and `len(range(3)) == 3` |

Chosen: H1. **Abandoned: H2, H3, H4** — each predicts an observable that does not occur.

**Stage 3 — Adversarial simulation**

- Counterexample test: does `[lambda i=i: i for i in range(3)]` print 0, 1, 2? Yes. That confirms the cause is the *binding*, not `lambda` itself — a default argument is evaluated at definition time and creates a fresh binding per iteration.
- Second trap: the same bug appears in a generator expression, `(lambda: i for i in range(3))`. The fix must be described as "bind per iteration", not "avoid lambda".
- Third trap: `functools.partial` binds immediately and is also correct — useful to mention so the fix does not look like a syntactic trick.
- Precision check: the common phrasing "Python closures capture by value" is exactly backwards. The output is only explicable if capture is by reference.

**Stage 4 — Structured output**

### Output

> The three lambdas share one variable, and it is `i` itself — not a copy of its value.
>
> `lambda: i` is a closure over the name `i`. It keeps a reference to the enclosing cell and reads whatever that cell holds *at the moment the lambda is called*. The loop finishes before any call happens, so the cell holds 3, and all three closures read the same 3.
>
> The output proves this without needing the theory: during the loop `i` only ever takes 0, 1, 2. A printed 3 is outside that range, so the value was read after the loop ended.
>
> Bind per iteration instead:
>
> ```python
> funcs = [lambda i=i: i for i in range(3)]
> ```
>
> A default argument is evaluated when the lambda is created, so each closure gets its own copy. `functools.partial` and a factory function achieve the same thing.
>
> Two things this is not: it is not a `print` buffering artifact — buffering reorders output, it does not change values — and it is not specific to `lambda`. The identical bug appears inside a generator expression, so the rule to remember is "bind per iteration", not "avoid `lambda`".
>
> The usual phrasing "Python closures capture by value" is backwards; this behaviour is only explicable if capture is by reference.

### What to copy

The extremum probe on the *observable* — noticing that 3 is outside the possible range — is what makes the diagnosis airtight instead of merely plausible. Look for a number in the output that should not be there.

---

## Walkthrough 3 — A scheduling optimization

**Problem:** Four people must cross a bridge at night. They have one torch, at most two may cross at a time, and anyone crossing must carry the torch. Their crossing times are 1, 2, 5, and 10 minutes; a pair moves at the slower person's pace. Minimize the total time.

### Internal derivation

**Stage 1 — Formalization and extremum probing**

- State: (set of people on the near side, torch side). Moves: forward with one or two people, backward with one or two.
- Person-trip count: everyone must end on the far side. Let r be the number of return trips. Forward person-trips = 4 + r, and forward trips carry 2 people each, so (4 + r)/2 must be an integer → r is even and ≥ 0. With r = 0 nobody can return the torch after the first crossing, impossible. So **r = 2**, giving exactly **3 forward trips** and **2 return trips**.
- Bounds: each return costs at least 1 (the fastest available person). So returns contribute ≥ 2. The trip carrying 10 costs ≥ 10.

**Stage 2 — Hypothesis tree and minimal conflict set**

| Path | Content | Confidence | Refutation condition |
|------|---------|-----------|---------------------|
| H1 | Fastest person shuttles: 1&2 →, 1 ←, 5&10 →, 2 ←, 1&2 → = 17 | High | Refuted if a plan under 17 exists |
| H2 | Send 1 with everyone: 1&10 →, 1 ←, 1&5 →, 1 ←, 1&2 → = 10+1+5+1+2 = 19 | High | Strictly worse; the 5 and 10 crossings cannot be shared |
| H3 | Pair the two slowest last, without the 2 ← return | — | Refuted by the trip count: with r = 2 the torch must come back twice, and after 5&10 cross only 1 remains near, so a return is forced |

Chosen: H1. **Abandoned: H2** (19 > 17), **H3** (violates the trip count from Stage 1).

**Stage 3 — Adversarial simulation**

- Optimality proof, not just a good plan: with exactly 3 forward and 2 return trips, the returners are drawn from people already across. Returning 5 or 10 instead of 1 or 2 costs strictly more, so assume the returners are the two fastest. The six forward person-slots are then {1, 1, 2, 2, 5, 10}. Since 5 and 10 must each cross, the pairings are either {1,2}, {1,5}, {2,10} → 2 + 5 + 10 = 17, or {1,2}, {1,10}, {2,5} → 2 + 10 + 5 = 17. Both give 17; any plan returning a slower person exceeds it.
- Trap: the greedy intuition "always send the fastest person back" is not optimal in general. It happens to be optimal here, but for times 1, 2, 5, 8 the shuttling strategy degrades.
- Trap: forgetting that a pair moves at the slower pace — that is what makes 5&10 cost 10 rather than 5.
- Ambiguity: "at most two" allows one person to cross alone; no plan needs it here, but the model should not exclude it silently.

**Stage 4 — Structured output**

### Output

> **17 minutes.**
>
> | Step | Move | Cost |
> |------|------|------|
> | 1 | 1 and 2 cross | 2 |
> | 2 | 1 returns | 1 |
> | 3 | 5 and 10 cross | 10 |
> | 4 | 2 returns | 2 |
> | 5 | 1 and 2 cross | 2 |
> | | **Total** | **17** |
>
> **Why nothing faster exists.** Let r be the number of return trips. Everyone must end across, so forward person-trips number 4 + r, and forward trips carry two people each — hence (4 + r)/2 must be a whole number, and r = 0 is impossible because the torch could never come back. The smallest solution is r = 2, i.e. exactly three forward trips and two returns.
>
> The returners must be people who are already across, and swapping a slow returner for a faster one only lowers the cost, so assume the two fastest return. The six forward slots are then {1, 1, 2, 2, 5, 10}. Since 5 and 10 must each cross, the pairings are either {1,2}, {1,5}, {2,10} = 2 + 5 + 10, or {1,2}, {1,10}, {2,5} = 2 + 10 + 5. Both total 17, and any plan that returns 5 or 10 costs strictly more. So 17 is the minimum, not merely the best plan found.
>
> Note that the pairing {5, 10} is what makes this work: sending them together costs 10 instead of 15, which is exactly enough to pay for the extra return trip.

### What to copy

H2 was a *plausible* plan that was strictly worse, and it was abandoned with a number rather than a feeling. And the answer does not stop at exhibiting 17 — it proves no smaller total exists, which is the difference between "a schedule" and "the optimum".

---

## Walkthrough 4 — A self-referential paradox

**Problem:** "This statement is false." Is it true or false?

### Internal derivation

**Stage 1 — Formalization and extremum probing**

- Let S denote the sentence. Its content asserts S ↔ ¬S.
- Truth-value space under classical bivalent semantics: {true, false}. Size 2, so an exhaustive case check is available — no approximation, no bound estimate needed. The probe here is the recognition that the search space is finite and tiny.

**Stage 2 — Hypothesis tree and minimal conflict set**

| Path | Content | Confidence | Refutation condition |
|------|---------|-----------|---------------------|
| H1 | S = true | — | Refuted: if S is true its content holds, so S is false. Contradiction |
| H2 | S = false | — | Refuted: if S is false its content fails, so S is not false, i.e. S is true. Contradiction |
| H3 | S has no truth value; it is not a proposition of classical logic | High | Survives both cases |

H1 and H2 are exhausted by the two-element space, so the minimal conflict set is empty: **no** assignment satisfies S. H3 survives and is therefore the answer.

Critical distinction: this is **not** an "insufficient conditions" verdict. Condition 3 of the gate is satisfied — the axioms are exhausted and there is no unique *truth value* — but the result is stronger than that: the non-existence of any assignment has been **proven**. A proof of non-existence is a conclusion, not a failure to conclude.

**Stage 3 — Adversarial simulation**

- Is H3 a cop-out dressed as sophistication? Test it against a contrast case: "This statement is true" asserts S ↔ S, which is satisfied by both S = true and S = false. That sentence is *underdetermined*, not paradoxical. The analysis therefore discriminates between two different diagnoses rather than stamping "paradox" on anything self-referential — which is what a cop-out would do.
- Second contrast: "This statement is not true" has the same structure as S and the same verdict, confirming the analysis tracks the structure rather than the wording.
- Scope trap: "no truth value" is the answer *in classical two-valued logic*. In strong Kleene three-valued logic the sentence evaluates to undefined; in paraconsistent logics it can be both true and false without explosion. Stating the classical answer without the scope is an overclaim.
- Trap: answering "it's a paradox" and stopping. That names the phenomenon instead of deriving it.

**Stage 4 — Structured output**

### Output

> Under classical two-valued logic the sentence has no truth value — and that is a provable result, not a failure to determine one.
>
> Let S be "S is false", so its content asserts S ↔ ¬S.
>
> - If S is true, its content holds, so S is false. Contradiction.
> - If S is false, its content fails, so S is not false — that is, S is true. Contradiction.
>
> True and false are the only two values available, so those cases are exhaustive: no assignment satisfies the sentence. S is therefore a well-formed sentence that is not a proposition of classical logic.
>
> The contrast with "This statement is true" shows the analysis is discriminating rather than a blanket "paradox" label. That sentence asserts S ↔ S, which is satisfied by both values, so it is merely underdetermined — a different diagnosis for a superficially similar form.
>
> The answer is also logic-dependent. In strong Kleene three-valued logic the sentence takes the value *undefined*; in paraconsistent logics it can be assigned both true and false without trivializing the system. "No truth value" is the classical verdict, not a universal one.

### What to copy

The gate in section 3 of the protocol is about *avoiding* conclusions. This case shows its mirror image: when non-existence is proven, the confident answer is "there is no such value", and that is the opposite of hiding behind "cannot determine". The two must not be confused in either direction.

---

## The Shape Common to All Four

| Property | Why it matters |
|----------|----------------|
| Stage 1 produces a **number or a bound** before any example is built | It converts a hunch into something falsifiable, and often predicts later refutations for free |
| Stage 2 lists paths that are actually **abandoned, with reasons** | A hypothesis tree where every branch survives is decoration |
| Stage 3 hunts for the **strongest objection**, not a token one | The negative-integer check, the generator-expression variant, the optimality proof, the contrast case — each is the objection that would actually break the answer |
| Stage 4 output contains **no stage labels** and is shorter than the derivation | The reader gets the logic chain, not the transcript |
| Every output ends with **coverage**, implicitly or explicitly | The self-check is what catches the constraint nobody addressed |

The most common way to fail these is not to skip a stage but to perform it: a Stage 1 with no actual bound, a Stage 2 whose branches all agree, a Stage 3 that finds nothing wrong, a Stage 4 that dumps everything.
