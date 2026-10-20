# Domain Recipes

The four stages are domain-agnostic, but what counts as a "bound" or a "counterexample" is not. This file specializes each stage per domain so the stages do real work instead of producing generic filler.

Use the table to translate the vocabulary, then the sections for a compact worked example.

| Domain | Stage 1 "formalization" | Stage 1 "extremum" | Stage 3 "counterexample" |
|--------|------------------------|--------------------|--------------------------|
| Coding | State, invariants, input domain | Complexity floor, size of the input space, the tightest failing case | The minimal input that breaks it; the boundary value |
| Mathematics | Symbols, quantifiers, what is given vs what is wanted | Largest constant that works; the tightest bound | A concrete number that falsifies the claim |
| Data analysis | Schema, grain of a row, what is measured vs derived | Base rate, sample size, plausible range of the effect | A confounder, a selection effect, a denominator error |
| Planning | Entities, constraints, objective | Lower bound on cost or time; the binding constraint | The constraint that makes the plan infeasible |
| Writing / argument | Claim, premises, what is being contested | The strongest opposing case | The reader who rejects the premise |

---

## Coding

**Stage 1.** Name the state and the invariant before touching the fix. "The invariant is that `i` indexes the last processed element" is formalization; "there's a bug in the loop" is not. Also fix the input domain — empty, single element, negative, maximum size. Most code bugs live at a domain boundary, so enumerating the domain *is* the extremum probe.

**Stage 2.** Hypotheses should be about *mechanisms*, not symptoms: late binding, aliasing, integer overflow, encoding, ordering, concurrency. Each must be refutable by an observation. A hypothesis that cannot be distinguished by any test is not a hypothesis.

**Stage 3.** The strongest counterexample is the **minimal failing input**. Shrink the case until it is one line. Then ask what the *correct* behaviour would be on that input — if you cannot state it, the specification is the actual bug.

**Stage 4.** Give the cause, the fix, and the reason the fix works. Do not narrate the search.

### Worked example

**Problem:** A pagination endpoint returns duplicate rows across pages.

> **Stage 1.** State: (offset, limit, underlying row order). Invariant needed: the row order must be total and stable across queries. Extremum probe: the smallest failing case is a 3-row table with limit 2 — page 1 returns rows 1,2; page 2 returns rows 2,3. That shape points at ordering, not at arithmetic.
>
> **Stage 2.** H1: `OFFSET` arithmetic is wrong — refuted, since the offsets 0 and 2 are correct for limit 2. H2: the ordering is non-deterministic (`ORDER BY created_at` where several rows share a timestamp) — survives, and predicts exactly this overlap. H3: the client requests overlapping pages — refuted, the requests are 0 and 2.
>
> **Stage 3.** Counterexample to H2: add a unique tiebreaker and the duplicates vanish. Counterexample to the *fix*: if the tiebreaker is mutable, a concurrent update can still shift rows — so the fix must be a stable, immutable key.
>
> **Stage 4 (output).**
>
> > The rows are not being ordered consistently between the two queries. `ORDER BY created_at` is only a partial order — several rows share the same timestamp — so the database may return them in either sequence, and the `OFFSET` then lands on different rows each time.
> >
> > Add an immutable tiebreaker so the order is total:
> >
> > ```sql
> > ORDER BY created_at DESC, id DESC
> > ```
> >
> > This is why the duplication only shows up on busy pages: with fewer than `limit` rows per timestamp the partial order happens to be stable, and the bug is invisible.

---

## Mathematics

**Stage 1.** Separate given from wanted explicitly, and pin the quantifiers — "for all n" versus "there exists n" changes the problem entirely. The extremum probe means asking for the **tightest** bound: the largest constant, the smallest exponent, the exact threshold. A proof of a non-tight bound is usually easier and much less useful.

**Stage 2.** Candidate paths are proof strategies: direct, contradiction, induction, construction, counting, invariant. Each has a characteristic failure mode, which makes the refutation column easy to fill — induction fails when the step does not close, contradiction fails when the negation is not exploitable.

**Stage 3.** Test at n = 0, 1, 2 and at negative values. Test whether the claim survives being strengthened — if it does, the proof probably proved more than was asked, which is fine, but if the strengthened claim is false the original bound was not tight.

**Stage 4.** State the result, the argument, and the tightness. A proof without tightness leaves the reader unable to tell whether the bound was found or guessed.

### Worked example

**Problem:** Is 2ⁿ > n² for all positive integers n?

> **Stage 1.** Quantifier: for all n ≥ 1. Extremum probe: check small values — n=1: 2 > 1 ✓; n=2: 4 = 4 ✗. The claim is **false** at n = 2, and also at n = 4 (16 = 16). So the bound n ≥ 1 is wrong; the interesting question is the exact threshold.
>
> **Stage 2.** H1: the claim is false — survives immediately with the counterexample n = 2. H2: the claim holds for n ≥ 5 — survives, and is the correct repaired statement. H3: the claim holds for n ≥ 3 — refuted by n = 4.
>
> **Stage 3.** Counterexample to H2: none exists; induction from n = 5 works because 2ⁿ⁺¹ = 2·2ⁿ > 2n² ≥ (n+1)² for n ≥ 3. Boundary check: n = 5 gives 32 > 25 ✓. Tightness: n = 4 is exactly the last failure, so 5 is the minimal valid threshold.
>
> **Stage 4 (output).**
>
> > No. The claim fails at n = 2 (4 = 4, not greater) and again at n = 4 (16 = 16). It holds for every n ≥ 5, and 5 is the smallest such threshold.
> >
> > For n ≥ 5, induction works: 2⁵ = 32 > 25 = 5². If 2ⁿ > n², then 2ⁿ⁺¹ = 2·2ⁿ > 2n², and 2n² ≥ (n+1)² whenever n² − 2n − 1 ≥ 0, i.e. n ≥ 1 + √2 ≈ 2.41. So the step closes for all n ≥ 3, and starting from n = 5 covers the whole range.
> >
> > The failures at 2 and 4 are exactly the points where the two sides tie, which is why the inequality has to be strict and why the threshold is 5 rather than 3.

---

## Data Analysis

**Stage 1.** Establish the **grain** first — what one row represents. Most analysis errors are grain errors: counting rows when you meant to count users, averaging averages, joining on a non-unique key. Then separate measured from derived columns.

**Stage 2.** Hypotheses about *why a number could be wrong*, not about what the number means: selection effect, survivorship, Simpson's paradox, denominator change, timezone boundary, double counting. Each is refutable by a specific query.

**Stage 3.** The strongest counterexample is a **confounder that reverses the sign** of the effect. If you can construct a plausible one, the finding is not yet safe to report.

**Stage 4.** Lead with the number and its denominator, then the caveat that would change the conclusion.

### Worked example

**Problem:** Conversion rate rose from 2.1% to 2.4% after a redesign. Did the redesign work?

> **Stage 1.** Grain: one row per session. Rate = conversions / sessions. Extremum probe: a 0.3pp change on a small base is noise — what sample size is required? For a 2.1% → 2.4% lift at 80% power, roughly 25,000 sessions per arm are needed.
>
> **Stage 2.** H1: genuine improvement — survives, conditional on sample size. H2: mix shift (the traffic composition changed, e.g. more returning users after the redesign) — survives. H3: definition change (a new event was added to the conversion count) — must be checked against the event schema. H4: seasonality — refuted if a holdout period exists.
>
> **Stage 3.** Counterexample to H1: split by user type. If returning users convert at 4% and their share rose from 20% to 30%, the blended rate rises with *no* per-segment improvement — Simpson's paradox. That single split can reverse the conclusion, so it must be run before reporting.
>
> **Stage 4 (output).**
>
> > Not yet — the 2.1% → 2.4% change is consistent with no improvement at all.
> >
> > Two things need checking first. **Sample size:** detecting a 0.3pp lift at that base rate needs roughly 25,000 sessions per arm; below that, a move this size is within normal variation. **Mix shift:** if returning users convert at ~4% and their share of traffic rose, the blended rate rises even with every segment flat. Split by user type and recompute; if both segments are unchanged, the redesign did nothing and the aggregate moved because the population did.
> >
> > If both segments did improve, then the finding is real and the remaining question is whether the effect persists past the novelty window — a two-week comparison against the holdout answers that.

---

## Planning and Scheduling

**Stage 1.** List entities, constraints, and the objective as three separate things — most planning failures come from a constraint smuggled into the objective. The extremum probe is a **lower bound on cost or time** and an identification of the **binding constraint**: which single constraint, if relaxed, would most improve the answer.

**Stage 2.** Hypotheses are plan families: greedy, batching, parallel, deferred. Each is refutable by a cost comparison or by an infeasibility argument.

**Stage 3.** Hunt for the constraint that makes the plan infeasible, not the one that makes it suboptimal. Infeasibility is fatal; suboptimality is a trade-off to be named.

**Stage 4.** Give the plan, its cost, the proof that nothing cheaper exists (or an explicit statement that optimality is not established), and the binding constraint.

### Worked example

**Problem:** Ship three features in six weeks with two engineers and one shared staging environment.

> **Stage 1.** Entities: 3 features, 2 engineers, 6 weeks, 1 staging slot. Constraint: staging is exclusive — two features cannot be integration-tested simultaneously. Lower bound: each feature needs at least one integration pass of ~2 days, and passes cannot overlap, so ≥ 6 days of the 30 engineer-days are serialized. Binding constraint: staging exclusivity, not engineer capacity.
>
> **Stage 2.** H1: parallel development, serialized integration — feasible, cost ≈ 6 days of queueing. H2: fully parallel including staging — infeasible, violates exclusivity. H3: sequential features — feasible but wastes one engineer for most of the window.
>
> **Stage 3.** Counterexample to H1: if a feature's integration fails and needs a second pass, the queue grows and the last feature may miss the deadline. So H1 is feasible only with a buffer, and the buffer must come from the integration schedule rather than from engineering time.
>
> **Stage 4 (output).**
>
> > Feasible, but only if staging is scheduled as the critical resource rather than treated as shared infrastructure.
> >
> > The binding constraint is the single staging slot: integration passes cannot overlap, so at least three sequential passes of about two days each are forced. Engineer time is not the bottleneck — 30 engineer-days against roughly 24 days of work.
> >
> > Run all three features in parallel for development, then serialize integration in the order of highest schedule risk first, reserving one extra pass as a buffer. That buffer is the part that matters: if any feature needs a second integration pass, a plan without it will push the third feature past week six, and the failure will look like an engineering delay when it is actually a queueing effect.

---

## Writing and Argument

**Stage 1.** State the claim in one sentence, and separate it from the evidence offered for it. The extremum probe is the **strongest opposing case**: what is the best argument against this claim? If the draft has no answer to it, the draft is not finished.

**Stage 2.** Hypotheses about where the argument is weak: an unstated premise, a term used in two senses, a correlation read as causation, an example that does not generalize.

**Stage 3.** The counterexample is the reader who accepts every premise and still rejects the conclusion. That reader is the one worth writing for.

**Stage 4.** Claim first, then the support, then the strongest objection and its answer. Burying the claim under context is the most common structural failure.

### Worked example

**Problem:** "Remote work is more productive. Our team shipped 30% more last quarter after going remote. Therefore we should stay remote permanently."

> **Stage 1.** Claim: remote work is more productive. Evidence: a 30% shipping increase in one quarter. Extremum probe: the strongest opposing case is that the increase came from something else correlated with the transition — a new hire, a deadline, or a lower baseline from the previous quarter.
>
> **Stage 2.** H1: the causal claim holds — weakened by n = 1 team, one quarter. H2: the increase is confounded — survives. H3: "shipped 30% more" measures output volume, not value — survives and undermines the productivity term specifically.
>
> **Stage 3.** Counterexample to the conclusion: a team that shipped more but of lower value is less productive by any business measure, so volume alone cannot support "more productive". Also: one quarter cannot separate the transition effect from the steady state.
>
> **Stage 4 (output).**
>
> > The conclusion outruns the evidence. "Shipped 30% more" is a volume measure over a single quarter for a single team, and three separate things could produce it without remote work being more productive: a deadline that pulled work forward, a lower baseline in the comparison quarter, or more items shipped at lower value each.
> >
> > The claim also needs "more productive" defined. If it means more output per hour, hours are not measured here. If it means more value, value is not measured here.
> >
> > What the evidence does support is narrower and still useful: *this team shipped more items in the quarter after going remote.* That is worth acting on — but as a reason to keep testing remote work, not as a reason to make it permanent. A second quarter and a value-weighted measure would turn it into evidence for the stronger claim.

---

## Cross-Domain Note

The recipes differ in vocabulary, not in structure. In every domain:

- Stage 1 produces something **falsifiable** — a bound, a grain, a lower bound, an opposing case.
- Stage 2 produces at least one **abandoned** branch.
- Stage 3 produces the objection that would **actually break** the answer.
- Stage 4 produces an output **shorter** than the derivation, with no labels.

If a domain-specific answer skips one of these, the domain did not change the protocol — the protocol was skipped.
