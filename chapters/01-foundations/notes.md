# Chapter 1 — Foundations: Sample Spaces, Counting, Probability Axioms, Conditional Probability

> *"Probability is a measure on a sample space; everything else is bookkeeping."*

You already know from high-school stats that a fair coin lands heads with "probability 1/2" and that a histogram shows how often each outcome occurs. This chapter asks the next question: **what does the word *probability* actually mean, and what rules does it obey?** The answer — three short axioms, one definition of conditioning, one formula called Bayes' rule — is enough to solve every puzzle in the rest of the chapter, and most of the confusion in popular-press reporting of test results, election polls, and medical studies.

---

## §1.0 The anchor problem — the hospital's "60% false positive" headline

A news item crosses your feed:

> *"A recent audit found that 60% of positive COVID tests at City Hospital are false positives."*

Your friend is horrified. The test itself, she says, has **99% sensitivity** (99% of people who have COVID test positive) and **99% specificity** (99% of people without COVID test negative). How can such an accurate test be wrong 60% of the time?

Before reading on, write down your guess for what's going on. Is the test broken? Is the headline wrong? Is something else happening?

By §1.12 we'll have the tools to answer this cleanly. The surprise is that **both the headline and the test's sensitivity/specificity numbers can be correct** — and the thing that reconciles them is one of the most consequential identities in all of statistics: Bayes' rule. We will derive it from nothing, apply it to the hospital, and see that the "60% false positive" number is not a critique of the test — it's a statement about who is getting tested.

---

## §1.1 Notation we'll use throughout

We assume high-school stats (mean, median, variance concept, fair coin/die probabilities, reading a histogram). Everything above that we define here.

- **Set.** An unordered collection of distinct items, written in curly braces: `{H, T}` is the set of coin-flip outcomes, `{1, 2, 3, 4, 5, 6}` the set of die-roll outcomes. If there are many items we use "…" to abbreviate: `{1, 2, …, 100}`.
- **Membership.** We write `x ∈ A` for "`x` is an element of `A`," and `x ∉ A` for "`x` is not."
- **Empty set.** `∅` is the set with no elements.
- **Subset.** `A ⊆ B` means every element of `A` is also an element of `B`. `A = B` iff `A ⊆ B` and `B ⊆ A`.
- **Union, intersection, complement, difference.** For two sets `A, B`:
  - `A ∪ B = {x : x ∈ A or x ∈ B}` (at least one).
  - `A ∩ B = {x : x ∈ A and x ∈ B}` (both).
  - `Aᶜ = {x : x ∈ Ω and x ∉ A}` (everything *not* in `A`, relative to the universe Ω we're working in).
  - `A ∖ B = A ∩ Bᶜ` (in `A` but not `B`).
- **Disjoint.** `A` and `B` are *disjoint* if `A ∩ B = ∅`. More generally, a collection of sets is *pairwise disjoint* if every pair among them is disjoint.
- **Cardinality.** `|A|` is the number of elements of `A`. For the sample space of a die, `|Ω| = 6`.
- **Summation over a set.** We often write `Σ_{ω ∈ A} f(ω)` to mean "sum `f(ω)` over every `ω` in the set `A`." This generalizes the `Σ_{i=1}^n f(i)` you've seen before — the index is now free to range over any set, not just integers 1..n.
- **Probability.** We will write `P(A)` for "the probability of the event `A`." Right now that's just notation; §1.6 defines what it means.

The Greek letters we'll use are: `Ω` ("omega," the sample space), `ω` ("little omega," an outcome — one element of Ω), `Σ` ("capital sigma," a sum). Nothing fancy.

One last piece of logic vocabulary: **"iff"** means "if and only if." `P iff Q` is the statement that `P` and `Q` are equivalent — each implies the other.

---

## §1.2 Sample spaces and events

We want to talk about uncertain experiments — things whose outcome is unknown before we run them. A **random experiment** is any procedure with a well-defined set of possible outcomes: flipping a coin, rolling a die, drawing a card, sampling a patient from a registry and recording whether their COVID test is positive.

**Definition (Sample space).** The **sample space** `Ω` of a random experiment is the set of all possible outcomes. An element `ω ∈ Ω` is an **outcome**.

- Coin flip: `Ω = {H, T}`.
- Two coin flips: `Ω = {HH, HT, TH, TT}`.
- Die roll: `Ω = {1, 2, 3, 4, 5, 6}`.
- Waiting time for a bus: `Ω = [0, ∞)` — a continuous set.
- COVID test result on a patient: `Ω = {(D+, T+), (D+, T−), (D−, T+), (D−, T−)}`, four outcomes recording (Disease status, Test result). We'll use this throughout the chapter.

**Definition (Event).** An **event** is a subset of `Ω`. The event "the die rolled even" is `{2, 4, 6}`. The event "the coin flip was heads" is `{H}`. The event "the test came back positive" is `{(D+, T+), (D−, T+)}`.

The picture Blitzstein and Hwang use throughout their Ch 1 is **Pebble World** (Blitzstein pp. 3–5): imagine the sample space `Ω` as a rectangle filled with little pebbles, one per outcome. An event is a blob drawn around some of those pebbles. Operations on events are just operations on blobs: `A ∪ B` is the union of two blobs; `A ∩ B` is their overlap; `Aᶜ` is everything in the rectangle outside the blob.

Two events worth naming explicitly:
- **The certain event** is `Ω` itself (the whole rectangle).
- **The impossible event** is `∅` (no pebbles).

---

## §1.3 The naive definition of probability

When the sample space is finite and "every outcome is equally likely," high-school stats gives us the formula:

> `P(A) = |A| / |Ω|` — favorable outcomes divided by total outcomes.

This is the **naive definition of probability** (Blitzstein p. 6). It is correct in a narrow setting — fair coin, fair die, well-shuffled deck — and it is misleading elsewhere. Two warnings up front:

1. **"Equally likely" is a physical assumption, not a definition.** When we say "a fair die has `P({6}) = 1/6`," we're asserting symmetry of the physical die. If the die is loaded, the formula still lets you compute `|A|/|Ω|`, but that quantity is no longer the probability.
2. **Many natural sample spaces don't even have a finite `Ω`.** Waiting times, temperatures, true patient parameters — these live in `[0, ∞)` or `ℝ`, and `|Ω|/|Ω|` is `∞/∞`. We need a different definition, coming in §1.6.

For now the naive definition is a useful warm-up because most counting problems in Ch 1 assume equally likely outcomes, so the whole probability question reduces to: *how many pebbles in `A`, how many in `Ω`?* That is a **counting** problem. The next two sections solve it.

---

## §1.4 Counting I: the multiplication rule, permutations, and combinations

**Multiplication rule (Blitzstein p. 8).** If experiment A has `a` possible outcomes and, regardless of which outcome A gives, experiment B has `b` possible outcomes, then the pair `(A, B)` has `a · b` possible outcomes. Equivalently, if you draw a tree with `a` branches out of the root and `b` branches out of each of those, there are `a · b` leaves.

- Two coin flips: `|Ω| = 2 · 2 = 4`. ✓
- A three-digit PIN using digits 0–9: `10 · 10 · 10 = 1000`. ✓
- Choose a shirt (4 options), then pants (3), then shoes (2): `4 · 3 · 2 = 24` outfits.

**Permutations.** A **permutation** of a set is an arrangement of all its elements in order. The number of permutations of `n` distinct items is

```
n! = n · (n − 1) · (n − 2) · … · 2 · 1
```

(read "n factorial"). The derivation is the multiplication rule: there are `n` choices for the first slot, `n − 1` for the second (we've used one item), and so on. By convention `0! = 1`.

An ordered arrangement of **`k` distinct items out of `n`** has

```
n(n − 1)(n − 2) … (n − k + 1) = n! / (n − k)!
```

choices. This quantity is sometimes written `P(n, k)` or `(n)_k`.

**Combinations.** If we only care about *which* `k` items we chose (not the order), we've overcounted each subset by `k!` (the number of ways to permute the chosen items). Dividing gives

```
 ( n )         n!
 (   )  :=  ────────
 ( k )      k!(n−k)!
```

read "`n` choose `k`" — the number of `k`-element subsets of an `n`-element set. We'll write this `C(n, k)` in running text and use `choose(n, k)` in R code.

**Small-number sanity checks.**
- `choose(5, 0) = 1` — there is one empty subset.
- `choose(5, 5) = 1` — there is one full-set subset.
- `choose(5, 2) = 10` and `choose(5, 3) = 10` — symmetry, because choosing 2 to include is the same as choosing 3 to exclude.
- `choose(52, 5) = 2,598,960` — the number of five-card poker hands.

```R
# An R session to verify everything in this section:
choose(52, 5)                # 2598960
factorial(5)                 # 120
choose(5, 0:5)               # 1 5 10 10 5 1 — one row of Pascal's triangle
```

---

## §1.5 Counting II: overcounting, story proofs, binomial identities

A pattern you'll meet repeatedly: we count a set by an obvious method, then notice we've counted each element multiple times, and divide. Three quick examples.

**Arranging indistinguishable balls.** How many distinguishable arrangements of the letters `S`, `T`, `A`, `T` are there? Naively `4! = 24`, but the two T's are interchangeable, so each distinct arrangement has been counted `2! = 2` times. Answer: `24 / 2 = 12`.

**Poker: the number of "four of a kind" hands.** A four-of-a-kind hand is specified by (the rank that appears four times, the rank of the fifth card, and the suit of the fifth card): `13 · 12 · 4 = 624`. The probability of being dealt four of a kind is

```
P(four of a kind) = 624 / choose(52, 5) = 624 / 2,598,960 ≈ 0.00024.
```

**Story proofs.** A *story proof* (Blitzstein p. 19) establishes a combinatorial identity by explaining what both sides count. Example: the identity

```
choose(n, k) = choose(n − 1, k − 1) + choose(n − 1, k)
```

is true because the number of `k`-element subsets of `{1, 2, …, n}` equals the number that *include* element `n` (choose `k − 1` more from the remaining `n − 1`) plus the number that *exclude* element `n` (choose `k` from the remaining `n − 1`). This is Pascal's recurrence; it produces Pascal's triangle.

A second useful identity:

```
Σ_{k=0}^n choose(n, k) = 2^n
```

holds because the left side counts subsets of `{1, …, n}` grouped by size, and the right side counts them by "for each of the `n` elements, in or out." Both sides count the same thing: the total number of subsets.

```R
# Sanity-check Pascal's identity for n=8, k=3
choose(8, 3)                    # 56
choose(7, 2) + choose(7, 3)     # 21 + 35 = 56 ✓

# Sum identity
sum(choose(8, 0:8))             # 256 = 2^8 ✓
```

This is all the counting we need for Ch 1. Ch 3's binomial distribution will use the same tools.

---

## §1.6 The axioms of probability (Kolmogorov, 1933)

Counting gets us through finite sample spaces with equally likely outcomes. To handle non-uniform distributions (a loaded die), infinite sample spaces (waiting times), and to state general theorems, we need an abstract definition.

**Definition (Probability measure).** Given a sample space `Ω`, a **probability measure** `P` is a function that assigns a real number `P(A)` to every event `A ⊆ Ω`, subject to three axioms:

1. **Non-negativity.** `P(A) ≥ 0` for every event `A`.
2. **Normalization.** `P(Ω) = 1` — the certain event has probability 1.
3. **Countable additivity.** If `A₁, A₂, A₃, …` is a countable sequence of pairwise disjoint events (meaning `Aᵢ ∩ Aⱼ = ∅` whenever `i ≠ j`), then
   ```
   P(A₁ ∪ A₂ ∪ A₃ ∪ …) = P(A₁) + P(A₂) + P(A₃) + …
   ```

That's it. Every probability fact you'll meet in this curriculum follows from these three axioms. The triple `(Ω, P)` together — sample space plus probability measure — is what we mean by a **probability model**.

**A quick honesty note about σ-algebras.** When `Ω` is infinite (like an interval of the real line), not every subset can consistently be assigned a probability — this is a measure-theoretic subtlety, and the correct cure is to restrict `P` to a *σ-algebra* of "measurable" subsets. For every situation you'll meet in this curriculum, the events you'd naturally write down (intervals, unions of intervals, preimages of continuous functions) are measurable. We flag this here honestly and then proceed without dwelling — Wasserman §1.2 (pp. 3–5) gives the honest treatment if you want it.

### Verifying a toy model

For a fair die, set `P({k}) = 1/6` for `k = 1, 2, …, 6`, and extend to events by countable additivity: `P(A) = |A| / 6` for any `A ⊆ {1, …, 6}`.

- Non-negativity: `|A|/6 ≥ 0`. ✓
- Normalization: `P(Ω) = 6/6 = 1`. ✓
- Additivity: if `A` and `B` are disjoint subsets of `{1, …, 6}`, then `|A ∪ B| = |A| + |B|`, so `P(A ∪ B) = (|A| + |B|)/6 = P(A) + P(B)`. ✓

Every probability model you build in this course will admit the same three checks.

---

## §1.7 Consequences of the axioms

The three axioms pack a surprising amount of punch. Everything below is provable from them alone; we'll sketch the proofs to build the habit.

**Proposition 1.7.1.** `P(∅) = 0`.

*Proof.* `Ω = Ω ∪ ∅` with `Ω` and `∅` disjoint. By additivity, `P(Ω) = P(Ω) + P(∅)`, so `P(∅) = 0`. ∎

**Proposition 1.7.2 (Complement rule).** `P(Aᶜ) = 1 − P(A)`.

*Proof.* `Ω = A ∪ Aᶜ` with `A` and `Aᶜ` disjoint. So `1 = P(Ω) = P(A) + P(Aᶜ)`. ∎

**Proposition 1.7.3 (Monotonicity).** If `A ⊆ B`, then `P(A) ≤ P(B)`.

*Proof.* `B = A ∪ (B ∖ A)` with disjoint pieces. `P(B) = P(A) + P(B ∖ A) ≥ P(A)` because `P(B ∖ A) ≥ 0` by non-negativity. ∎

**Proposition 1.7.4 (Finite additivity).** Axiom 3 was stated for countable sequences. The *finite* version — `P(A ∪ B) = P(A) + P(B)` for disjoint `A, B` — follows by padding with `∅, ∅, ∅, …`.

These are workhorses. We'll use the complement rule repeatedly: *"it is easier to compute the probability of the complement than the probability itself."* Classic example: the birthday problem.

---

## §1.8 Inclusion–exclusion

The additivity axiom requires the events to be disjoint. What if they're not?

**Two-set inclusion–exclusion.**

```
P(A ∪ B) = P(A) + P(B) − P(A ∩ B)
```

*Proof sketch.* Write `A ∪ B = A ∪ (B ∖ A)` with disjoint pieces, so `P(A ∪ B) = P(A) + P(B ∖ A)`. Now `B = (B ∩ A) ∪ (B ∖ A)` with disjoint pieces, so `P(B) = P(A ∩ B) + P(B ∖ A)`, i.e. `P(B ∖ A) = P(B) − P(A ∩ B)`. Substituting gives the claim. ∎

**Three sets.**

```
P(A ∪ B ∪ C) = P(A) + P(B) + P(C)
              − P(A ∩ B) − P(A ∩ C) − P(B ∩ C)
              + P(A ∩ B ∩ C).
```

The pattern generalizes to `n` sets (alternating single, pair, triple, …) and is called **inclusion–exclusion** (Blitzstein §1.6, pp. 22–27). We'll use it later for the "at least one" problems where direct counting gets awkward.

---

## §1.9 Conditional probability — the definition

We have a probability measure on `Ω`. Suppose we now **learn** that some event `B` has occurred — what does that say about `A`?

**Definition.** For events `A, B` with `P(B) > 0`, the **conditional probability of A given B** is

```
                 P(A ∩ B)
     P(A | B) = ───────────.
                   P(B)
```

Read aloud: "the probability of `A` given `B`." Blitzstein (p. 46) gives the Pebble-World interpretation: the sample space shrinks to `B`, and we renormalize so `P(B | B) = 1`.

**Three small observations.**

1. **Conditional probability is a probability.** Fix `B` with `P(B) > 0`. Define `Q(A) := P(A | B)`. Then `Q` satisfies all three axioms (non-negativity is clear; `Q(Ω) = P(Ω ∩ B)/P(B) = P(B)/P(B) = 1`; countable additivity is inherited). So every theorem we've proved about probabilities is also true about conditional probabilities. We'll use this often.
2. **`P(A | B)` is NOT the same as `P(B | A)`.** Swapping the conditioning is one of the most common real-world mistakes (prosecutor's fallacy, "99% accurate test" paradoxes). Bayes' rule (§1.11) is precisely the tool for relating the two.
3. **When `P(B) = 0`, `P(A | B)` is undefined** by this formula. Ch 4 will give a more general definition that handles the continuous case, but for Ch 1 we always assume `P(B) > 0` when we condition.

---

## §1.10 The multiplication rule and the law of total probability

Rearranging the definition of conditional probability gives the **multiplication rule**:

```
P(A ∩ B) = P(A | B) · P(B) = P(B | A) · P(A).
```

This is useful in both directions: when you know the conditional and the marginal, you can compute the joint; when you know the joint and the marginal, you can compute the conditional.

**Partition.** A collection `B₁, B₂, …, Bₙ` of events is a **partition of `Ω`** if they are pairwise disjoint and `B₁ ∪ B₂ ∪ … ∪ Bₙ = Ω`. Think: every pebble in Ω belongs to exactly one `Bᵢ`.

**Law of total probability (LOTP).** If `B₁, …, Bₙ` partition `Ω` and each `P(Bᵢ) > 0`, then for any event `A`,

```
P(A) = Σᵢ P(A ∩ Bᵢ) = Σᵢ P(A | Bᵢ) · P(Bᵢ).
```

*Proof.* `A = A ∩ Ω = A ∩ (∪ᵢ Bᵢ) = ∪ᵢ (A ∩ Bᵢ)`, a disjoint union. By additivity, `P(A) = Σᵢ P(A ∩ Bᵢ)`. Each term equals `P(A | Bᵢ) · P(Bᵢ)` by the multiplication rule. ∎

Example to fix ideas. A factory has two machines. Machine 1 makes 60% of widgets, Machine 2 makes 40%. Machine 1 has a 2% defect rate, Machine 2 has a 5% rate. What fraction of widgets are defective?

```
P(defect) = P(defect | M1) P(M1) + P(defect | M2) P(M2)
          = 0.02 · 0.60 + 0.05 · 0.40
          = 0.012 + 0.020
          = 0.032 = 3.2%.
```

---

## §1.11 Bayes' rule

From the multiplication rule we have *two* expressions for `P(A ∩ B)`:

```
P(A | B) P(B) = P(B | A) P(A).
```

Solve for `P(A | B)`:

```
                 P(B | A) · P(A)
     P(A | B) = ──────────────────.
                     P(B)
```

That's **Bayes' rule** — or Bayes' theorem, the names are used interchangeably. Spoken in the language of statistical inference:

- `P(A)` is the **prior** — what we believed about `A` before we saw `B`.
- `P(B | A)` is the **likelihood** — how expected `B` was, given `A`.
- `P(A | B)` is the **posterior** — what we believe about `A` after seeing `B`.
- `P(B)` is the **marginal likelihood** (or "evidence") — the unconditional probability of the data. It is usually computed from the LOTP:

```
P(B) = P(B | A) P(A) + P(B | Aᶜ) P(Aᶜ).
```

Read from right to left, Bayes' rule says **posterior ∝ likelihood × prior**, with the marginal likelihood acting as a normalizer so the posterior sums to 1. Ch 10 will come back to this with priors that are distributions over parameters instead of single events. For Ch 1, we'll apply it to exactly one thing: our COVID anchor problem.

---

## §1.12 Solving the anchor problem

Recall the claim: "60% of positive COVID tests at City Hospital are false positives," combined with 99% sensitivity and 99% specificity. Let's set up the events.

- Let `D+` mean "the patient has COVID," `D−` mean "doesn't." These partition the sample space.
- Let `T+` mean "the test is positive," `T−` mean "negative."

The clinical numbers translate to:

- **Sensitivity:** `P(T+ | D+) = 0.99`.
- **Specificity:** `P(T− | D−) = 0.99`, so `P(T+ | D−) = 0.01`.

The claim is about `P(D− | T+)` — given a positive test, the probability the patient does *not* have COVID. That's the **false-positive rate among positives**, distinct from the test's per-subject specificity.

By Bayes' rule,

```
                  P(T+ | D−) · P(D−)
P(D− | T+)  =  ──────────────────────────────────────────────
               P(T+ | D−) · P(D−) + P(T+ | D+) · P(D+)

              0.01 · P(D−)
          =  ─────────────────────────────────.
             0.01 · P(D−) + 0.99 · P(D+)
```

This depends on `P(D+)` — the **prior probability that a tested patient has COVID**, also called the **prevalence in the tested population**. Let's plug in a few values:

| Prevalence `P(D+)` | `P(D− | T+)` | Conclusion |
|---|---|---|
| 10% | `0.01·0.9 / (0.01·0.9 + 0.99·0.1) = 0.009/0.108 ≈ 0.0833` | Mostly real positives |
| 1% | `0.01·0.99 / (0.01·0.99 + 0.99·0.01) = 0.0099/0.0198 = 0.5` | Half of positives are false! |
| 0.5% | `0.01·0.995 / (0.01·0.995 + 0.99·0.005) = 0.00995/0.01490 ≈ 0.6678` | 66.8% false positives |
| 0.1% | `0.01·0.999 / (0.01·0.999 + 0.99·0.001) = 0.00999/0.01098 ≈ 0.9098` | 91% false positives |

**So the hospital's "60% false positive" headline is compatible with a 99%/99% test, in a low-prevalence population (roughly 0.5–1%).** The test is fine. What the headline is really about is that **in a low-prevalence population, even a highly specific test produces more false positives than true positives among the positives** — because there are so many more healthy people than sick people that the 1% of healthy people who mistakenly test positive can outnumber the 99% of sick people who correctly test positive.

Two takeaways that will resurface throughout the curriculum:

- **Base rates matter.** Conditional probabilities depend on the prior `P(D+)`. Ignoring the base rate is called the **base-rate fallacy**, and it is catastrophically common.
- **`P(T+ | D+)` and `P(D+ | T+)` are different quantities.** Journalists routinely report one while implying the other.

```R
# Verify the 1%-prevalence row of the table above.
prior     <- 0.01            # P(D+)
sens      <- 0.99            # P(T+ | D+)
spec      <- 0.99            # P(T- | D-)
p_tpos    <- sens * prior + (1 - spec) * (1 - prior)
p_dneg_tpos <- (1 - spec) * (1 - prior) / p_tpos
p_dneg_tpos                  # 0.5
```

---

## §1.13 Independence

Sometimes learning `B` *doesn't* change what we believe about `A`. That's the content of independence.

**Definition.** Events `A` and `B` are **independent**, written `A ⫫ B`, if

```
P(A ∩ B) = P(A) · P(B).
```

Equivalently (when `P(B) > 0`), `P(A | B) = P(A)`. That's the intuitive statement — conditioning on `B` doesn't update your belief about `A`.

- Two flips of a fair coin: `P(HH) = 1/2 · 1/2 = 1/4`. The flips are independent. ✓
- Drawing two cards *without replacement*: the first-card outcome and second-card outcome are NOT independent — once the first card is gone, the distribution of the second changes. Drawing *with replacement* makes them independent.

**Three-event independence** requires more than pairwise. Events `A, B, C` are **jointly (mutually) independent** if all the following hold:

```
P(A ∩ B) = P(A) P(B)
P(A ∩ C) = P(A) P(C)
P(B ∩ C) = P(B) P(C)
P(A ∩ B ∩ C) = P(A) P(B) P(C).
```

It is possible for three events to be **pairwise** independent but not jointly (Blitzstein p. 65 has a classic example with three flips and their XOR). We will explicitly say "pairwise" or "mutually" when it matters.

**Conditional independence.** `A` and `B` are **conditionally independent given `C`** if

```
P(A ∩ B | C) = P(A | C) · P(B | C).
```

Conditional independence does not imply independence — a point that matters for Simpson's paradox (next section) and for basically everything in Ch 10 (hierarchical models).

---

## §1.14 Classic pitfalls (Monty Hall, Simpson's paradox, prosecutor's fallacy)

### Monty Hall

Three doors. Behind one is a car; behind the others, goats. You pick door 1. The host, who knows where the car is, opens door 3 and reveals a goat. Should you switch to door 2?

**Intuition-first reasoning.** Your initial pick had a 1/3 chance of being right. If it was right (probability 1/3), switching loses. If it was wrong (probability 2/3) — which means the car is behind door 2 or 3 — the host is *forced* to open the goat-door among those two, and the remaining closed door is the car. So switching wins with probability 2/3.

**Bayesian re-derivation.** Let `C_i` = "car is behind door `i`," and let `H_3` = "host opens door 3." Then

- `P(H_3 | C_1)` = 1/2 (host can open 2 or 3, chooses randomly).
- `P(H_3 | C_2)` = 1 (door 1 is your pick; host must open 3 to avoid the car).
- `P(H_3 | C_3)` = 0 (host never opens the car's door).

By Bayes' rule,

```
                       P(H_3 | C_2) · P(C_2)
P(C_2 | H_3) = ─────────────────────────────────────────
               Σᵢ P(H_3 | C_i) · P(C_i)

               1 · 1/3
             = ─────────────────────
               1/2·1/3 + 1·1/3 + 0
             = (1/3) / (1/6 + 1/3)
             = (1/3) / (1/2)
             = 2/3.
```

Switching doubles your win rate from 1/3 to 2/3. The R notebook verifies this by simulating 100,000 games.

### Simpson's paradox

A treatment can appear *better* in every subgroup yet *worse* overall, once you pool the subgroups. Blitzstein pp. 76–80 walk through a real medical example with two doctors, mild cases, and severe cases.

The bookkeeping: if the *composition* of patients differs between treatments (A sees mostly severe cases, B sees mostly mild ones), the pooled recovery rates can flip the within-subgroup comparison — even when A is strictly better within each severity level.

The formal statement is about conditional vs. marginal probability:

```
P(recovery | treatment, severity)  ≠  P(recovery | treatment)
```

in general. Confounding by severity is the reason for randomized controlled trials. We'll return to this in Ch 8 ("lurking variables") and Ch 9 (multiple regression).

### The prosecutor's fallacy

Closely related to the COVID problem. A defendant's DNA "matches" at a 1-in-a-million level. A prosecutor claims: "There is only a 1-in-a-million chance the defendant is innocent." That is `P(innocent | match) = 10⁻⁶`, which is **not** the same as `P(match | innocent) = 10⁻⁶`. Given that the city has a million people, the expected number of *matching innocents* is around 1. The prosecutor has computed the likelihood and called it the posterior. Bayes' rule plus the base rate is the cure.

---

## §1.15 Summary and bridge to Chapter 2

In this chapter we built probability from nothing:

- A **sample space** `Ω` lists every possible outcome of a random experiment. An **event** is a subset of `Ω`.
- A **probability measure** `P` is a function assigning real numbers to events, subject to Kolmogorov's three axioms — non-negativity, normalization, countable additivity.
- When outcomes are equally likely, `P(A) = |A|/|Ω|`, and probability becomes counting.
- **Counting tools**: multiplication rule, permutations `n!/(n−k)!`, combinations `choose(n, k)`, inclusion–exclusion.
- **Conditional probability** `P(A | B) = P(A ∩ B)/P(B)` is a new probability measure on the "shrunk" sample space `B`. The **multiplication rule** and **law of total probability** follow.
- **Bayes' rule** — `P(A | B) = P(B | A) P(A) / P(B)` — inverts conditionals, turning a likelihood and a prior into a posterior. Base rates matter enormously.
- **Independence** `P(A ∩ B) = P(A) P(B)` means conditioning doesn't update beliefs.
- Classic pitfalls — Monty Hall, Simpson's paradox, the prosecutor's fallacy — are all base-rate / conditioning errors that Bayes' rule cleans up.

### What's next

Chapter 2 introduces **random variables** — functions `X : Ω → ℝ` that turn outcomes into numbers. Instead of asking "what is the probability of the event `{HHT, HTH, THH}`?" we'll ask "what is the probability that the number of heads in three flips equals 2?" That small shift is the move from counting to *distributions* — the Bernoulli, binomial, geometric, Poisson — which is what every downstream chapter (expectation, variance, LLN, CLT, estimators, tests, regression) is built on.

### Self-check before moving on

Can you, in one sentence each, state:

1. What a sample space and event are.
2. The three Kolmogorov axioms.
3. The definition of conditional probability and what it means geometrically.
4. Bayes' rule and the three pieces (prior, likelihood, posterior).
5. What "events `A` and `B` are independent" means.
6. Why the "60% of positives are false" news item is compatible with a 99%/99% test.

If yes — head to `worked-examples.md` and `exercises.md`. If no — re-read the relevant section. Blitzstein & Hwang Chs 1–2 and Wasserman Ch 1 are the page-for-page companions (see `sources.md`).
