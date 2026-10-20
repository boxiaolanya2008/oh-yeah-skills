# Insufficient Conditions

Section 3 of the protocol is a gate, not a phrase. It exists because "I cannot determine this" is the cheapest possible answer and therefore the one most likely to be produced for the wrong reason.

The gate has three conditions, and all three must hold:

1. Mode B has been entered;
2. In the Stage 2 hypothesis tree, the refutation conditions of every candidate path cannot be ruled out by the problem statement;
3. The explicit information and the standard axioms of the discipline are exhausted, and there is still no unique solution.

## The Reframe That Resolves Most Cases

Before reaching for the phrase, notice what condition 3 actually says. It does not say "I have no answer". It says "there is no **unique** solution".

In almost every case where the gate genuinely opens, the honest answer is not silence but a **precise characterization**: a solution set, a range, a family, an infeasibility proof, or an infimum that is not attained. That is a strictly stronger answer than "cannot determine", and it is what a top-tier reasoner produces.

The phrase "insufficient conditions" is therefore reserved for the residue: cases where even the *shape* of the answer set cannot be pinned down.

---

## Five Accepted Cases

### A1 — Contradictory constraints

**Problem:** Find an integer x with x > 5 and x < 3.

**Gate 1.** Mode B: feasibility of a constraint set is a provable claim.

**Gate 2.** The only candidate path is direct intersection of the two constraints. It yields the empty set, and nothing in the statement can rescue it — there is no path whose refutation condition is left unresolved.

**Gate 3.** Integer arithmetic is exhausted; no axiom admits an x in (3, 5) ∩ ℤ.

**Verdict.** No solution exists, and this is **proven**, which is exactly what the protocol demands of an unsolvable task.

**Output:**

> No such integer exists. x > 5 and x < 3 are jointly unsatisfiable over ℤ — the constraint set is empty, so this is an infeasibility result rather than an answer with a value.

Note that this is the *opposite* of hiding: the protocol requires unsolvability to be established, not assumed.

### A2 — A minimum that is not attained

**Problem:** Find x minimizing x² on the open interval (0, 1).

**Gate 1.** Mode B: an extremum under constraints.

**Gate 2.** Candidate paths are the interior critical point x = 0 (outside the domain) and the endpoints (excluded by openness). Both are ruled out by the statement itself. No surviving path.

**Gate 3.** The order axioms and the definition of an open interval are exhausted.

**Verdict.** No minimizer exists. The infimum is 0.

**Output:**

> There is no minimum. x² approaches 0 as x → 0⁺, so the infimum is 0, but 0 ∉ (0, 1) and every x in the interval gives x² > 0. The problem as posed asks for an object that does not exist.

The distinction between "no minimum" and "no infimum" is the whole content of the answer. Saying "cannot determine" would erase it.

### A3 — An underdetermined linear system

**Problem:** Solve x + y + z = 6, x − y = 0.

**Gate 1.** Mode B: logical deduction over a constraint system.

**Gate 2.** The paths are: unique solution (ruled out — three unknowns, two independent equations), no solution (ruled out — the system is consistent, e.g. (1,1,4)), infinite family (survives).

**Gate 3.** Linear algebra over ℝ is exhausted.

**Verdict.** Infinitely many solutions, one free parameter.

**Output:**

> The system is underdetermined: three unknowns, two independent equations, so the solution set is one-dimensional. From x = y = t, the third equation gives z = 6 − 2t. Every solution is (t, t, 6 − 2t) for t ∈ ℝ, and there is no unique answer.

### A4 — A shape with one degree of freedom

**Problem:** A rectangle has area 12. Find its perimeter.

**Gate 1.** Mode B: the question presupposes a determinate value.

**Gate 2.** Unique perimeter (ruled out — a = 1 gives 26, a = 3 gives 14), unbounded (survives), minimum (survives).

**Gate 3.** Elementary inequalities exhausted: P = 2(a + 12/a) ≥ 4√12 by AM–GM, with equality at a = √12.

**Verdict.** No unique perimeter; the achievable set is [4√12, ∞).

**Output:**

> There is no single perimeter — the area fixes only the product of the sides. With sides a and 12/a, the perimeter is 2(a + 12/a), which by AM–GM is at least 4√12 ≈ 13.86, attained at the square a = √12. It is unbounded above as a → 0 or a → ∞. So the perimeter ranges over [4√12, ∞) and the question needs one more constraint to have a single answer.

### A5 — Infinitely many interpolating polynomials

**Problem:** Find a polynomial with integer coefficients such that p(1) = 2, p(2) = 4, p(3) = 6.

**Gate 1.** Mode B: existence and uniqueness of a mathematical object.

**Gate 2.** Unique (ruled out — construct a second), none (ruled out — p(x) = 2x works), infinite family (survives).

**Gate 3.** Polynomial division and the factor theorem are exhausted: any two solutions differ by a multiple of (x−1)(x−2)(x−3).

**Verdict.** Infinitely many, and the family is exactly characterizable.

**Output:**

> p(x) = 2x is one solution, but it is not unique. If p and q both satisfy the conditions, then p − q vanishes at 1, 2, 3, so it is divisible by (x−1)(x−2)(x−3). Hence every solution is
>
> p(x) = 2x + (x−1)(x−2)(x−3)·r(x)
>
> for any polynomial r with integer coefficients. The three points pin down a quadratic at most; with integer coefficients required, they do not pin down a unique polynomial.

### What the five share

Every accepted case ends with a **positive structural statement**: empty set, infimum without minimum, parameterized family, interval, or a family generator. None ends with "I don't know". That is the standard the gate is meant to enforce.

---

## Six Rejected Cases

Each of these looks like a candidate for the phrase and is not.

### R1 — "Is 1000003 prime?"

**Gate 1 fails.** This is not Mode B reasoning, it is arithmetic. There is one path — trial division up to √1000003 ≈ 1000 — and it terminates.

Declaring insufficiency here is straightforwardly false: the answer is determinable, just tedious.

**Correct move:** compute it. 1000003 = 101 × 9901, so it is composite. If the number were larger, the correct move would be to state the method and its cost, not to refuse.

### R2 — "What's the best database for my app?"

**Gate 1 fails.** This is Mode A advice. No derivation was requested and no objective function was supplied.

**Correct move:** ask what the constraints are — read/write ratio, consistency needs, operational capacity, budget. The moment those arrive, the *next* turn may well be Mode B.

Confusing "the user has not told me their constraints" with "the problem is unsolvable" is the single most common misuse of the gate. The first calls for a question; the second calls for a proof.

### R3 — "What will this stock's price be tomorrow?"

**Gates 2 and 3 fail as stated.** It is true that no unique value can be derived, but the reason is not missing information — it is that the price is not a function of the available information at all.

**Correct move:** give the argument for unpredictability (the price is set by the aggregate of future order flow, which is not determined by past data), and then say what *can* be said — a distribution, a historical range, the drivers to watch. "Cannot determine" without the argument is a shrug; with the argument it is a result.

### R4 — "Translate 'He saw the man with the telescope.'"

**Gate 2 fails.** The ambiguity has exactly two readings, and both can be produced.

**Correct move:** give both translations and name the attachment ambiguity. A refusal here would be pure laziness — the answer set is finite, tiny, and fully enumerable.

### R5 — "How many people live in that city?"

**Gate 1 fails.** No city has been named, so the problem is not underdetermined, it is *incomplete*. The missing piece is an input, not a derivation.

**Correct move:** ask which city. Note the asymmetry: an incomplete problem is fixed by one question, whereas a genuinely insufficient problem cannot be fixed by any question because no answer exists.

### R6 — "Prove the Riemann Hypothesis."

**Gate 2 fails.** Hard is not the same as insufficient. There is a well-defined claim, a well-defined standard of proof, and an enormous body of partial results. No path can be *refuted* — they are simply unfinished.

**Correct move:** state what is known (verified for the first 10¹³ zeros, the connection to the distribution of primes), what the main attack strategies are, and where each stalls. Declaring insufficiency because a proof is beyond reach misrepresents the state of the discipline as a defect in the question.

---

## The Audit Trail

Before writing any insufficiency verdict, fill this in internally. If any line cannot be completed, the gate is not open.

```
Gate 1 — Mode B trigger:        <which trigger fired, e.g. "extremum under constraints">
Gate 2 — Paths and eliminations:
    H1 <content>  eliminated by: <specific statement in the problem or a computed value>
    H2 <content>  eliminated by: <...>
    H3 <content>  SURVIVES
Gate 3 — Axioms applied:        <the standard results used, and the residual gap>
Shape of the answer set:        <empty / range / family / infimum-only / genuinely unknown>
```

If the last line reads anything other than "genuinely unknown", do not use the phrase — output the shape instead.

## Three Diagnostic Questions

Ask these in order; each catches a different misuse.

1. **Could a question fix this?** If yes, ask it. Missing input is not insufficiency.
2. **Can I describe the answer set, even if I cannot name one element?** If yes, describe it. A characterized set beats a refusal.
3. **Would an expert in this field also say "cannot determine"?** If an expert would instead say "hard, here is the frontier", then say that.

## What the Verdict Should Look Like When It Is Genuine

When the gate truly opens, the output should still be informative. The pattern:

> The question as posed has no unique answer, and the reason is [specific structural reason]. [What the answer set is, if it can be characterized.] To get a single value you would need [the specific additional constraint that would close the gap].

That last clause is what separates a rigorous verdict from a shrug: it names exactly what is missing, which is itself a piece of analysis.
