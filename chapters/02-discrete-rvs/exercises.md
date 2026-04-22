# Chapter 2 — Exercises

> Ten practice problems for *Discrete Random Variables and Their Distributions*. Difficulty ramps: warm-up (1–3), standard application (4–7), push-yourself (8–10). Full answers are at the bottom.
>
> **Smoke-test discipline.** Every numerical answer below is verified by `/tmp/smoke_ch2.R` before publish. Try the exercises first — if your number disagrees with the answer key, run the smoke test to see whose arithmetic is right.

---

## Problems

### Exercise 1 (warm-up)

A single card is drawn from a standard 52-card deck. Let `X = 1` if the card is a heart, and `X = 0` otherwise. Identify the distribution of `X` and write its PMF.

### Exercise 2 (warm-up)

A basketball player makes 70% of her free throws. She shoots 10 free throws, each independent. What is the probability she makes **at least 8**?

### Exercise 3 (warm-up)

A biased coin comes up heads with probability 0.4. You flip until the first heads appears. What is the probability the first heads occurs on the **5th flip**?

### Exercise 4 (standard)

In a call center, 30% of calls are escalated to a specialist. Of 20 calls received today (assume independent), what is the probability that **exactly 5** are escalated?

### Exercise 5 (standard)

A jury pool of 20 candidates contains 12 women and 8 men. A jury of 12 is drawn **uniformly at random** from the pool. What is the probability that **all 12 jurors are women**?

### Exercise 6 (standard)

A published book averages 2 typographical errors per page. Assume typos on a page follow the Poisson paradigm. What is the probability that a randomly chosen page contains **at most 1 typo**?

### Exercise 7 (standard)

An archer hits the target with probability `p = 0.6` on each shot, independently. What is the probability that her **4th hit** occurs on the **7th shot**?

### Exercise 8 (push)

Coffee shop A serves customers according to a Poisson process at rate 3 customers/hour. Coffee shop B (across the street, independent of A) serves at rate 2 customers/hour. In the next hour, what is the probability that the **two shops together** serve exactly 5 customers?

### Exercise 9 (push)

Let `X ~ Binomial(4, 0.5)` and define `Y = |X - 2|` (the distance from the mean). Derive the PMF of `Y` — that is, `P(Y = 0)`, `P(Y = 1)`, `P(Y = 2)` — and verify that it sums to 1.

### Exercise 10 (push)

A pharmacy fills prescriptions according to a Poisson process at rate 10 per hour and has the staffing to process up to 15 per hour without delay. "Overload" means more than 15 prescriptions arrive in a given hour.

1. What is the probability the pharmacy is overloaded in **any given hour**?
2. Over an 8-hour workday (assume hours are independent), what is the probability the pharmacy is overloaded **at least once**?

---

## Answers

### Exercise 1 — answer

The deck has 13 hearts out of 52 cards, so `P(X = 1) = P(\text{heart}) = 13/52 = 1/4`. Therefore

```
X ~ Bernoulli(1/4),    P(X = 1) = 1/4,    P(X = 0) = 3/4.
```

This is an indicator of the event "card is a heart," so by §2.3, `X = 1_{heart} ~ Bernoulli(P(heart))`.

### Exercise 2 — answer

Let `X` = number of made free throws. Then `X ~ Binomial(10, 0.7)`.

```
P(X ≥ 8) = P(X = 8) + P(X = 9) + P(X = 10)
         = C(10, 8) · 0.7^8 · 0.3^2 + C(10, 9) · 0.7^9 · 0.3 + 0.7^10
         = 45 · 0.057648 · 0.09 + 10 · 0.040354 · 0.3 + 0.028248
         ≈ 0.23347 + 0.12106 + 0.02825
         ≈ 0.3828.
```

In R: `1 - pbinom(7, size = 10, prob = 0.7) ≈ 0.3828`. She makes ≥ 8 free throws about **38% of the time** — high mean (7.0) but wide spread (SD ≈ 1.45) means the tail is thick.

### Exercise 3 — answer

Let `X` = trial of the first heads. Under our "trial of first success" convention, `X ~ Geometric(0.4)`.

```
P(X = 5) = (1 - 0.4)^(5 - 1) · 0.4
         = 0.6^4 · 0.4
         = 0.1296 · 0.4
         = 0.05184.
```

So about **5.2%**. Intuition: the mean of Geometric(0.4) is `1/p = 2.5`, so seeing the first heads on flip 5 is "2 flips late" on average.

### Exercise 4 — answer

Let `X` = number of escalated calls. Each call is an independent Bernoulli(0.3), so `X ~ Binomial(20, 0.3)`.

```
P(X = 5) = C(20, 5) · 0.3^5 · 0.7^15
         = 15504 · 0.00243 · 0.0047476
         ≈ 0.17886.
```

In R: `dbinom(5, size = 20, prob = 0.3) ≈ 0.1789`. About **17.9%** — not far from the peak (the mean is `np = 6`).

### Exercise 5 — answer

Let `X` = number of women on the jury. `X ~ Hypergeometric(N = 20, K = 12, n = 12)`.

```
            C(12, 12) · C(8, 0)        1 · 1          1
P(X = 12) = ───────────────────── = ──────────── = ─────────
                 C(20, 12)           125970         125970

          ≈ 7.94 × 10^(-6).
```

In R: `dhyper(12, m = 12, n = 8, k = 12) ≈ 7.94e-06`.

Roughly **1 in 126,000** — extremely unlikely. A result this extreme would be strong evidence that the jury was **not** randomly drawn (a real issue that's come up in U.S. voting-rights cases). Ch 8's hypothesis-testing framework formalizes this kind of inference.

### Exercise 6 — answer

Let `X` = number of typos on a page. `X ~ Poisson(2)`.

```
P(X ≤ 1) = P(X = 0) + P(X = 1)
         = e^(-2) + 2 · e^(-2)
         = 3 · e^(-2)
         ≈ 3 · 0.13534
         ≈ 0.4060.
```

In R: `ppois(1, lambda = 2) ≈ 0.4060`. About **41%** of pages have at most 1 typo.

### Exercise 7 — answer

Let `Y` = shot number of the 4th hit. `Y ~ NegBinomial(r = 4, p = 0.6)`.

```
P(Y = 7) = C(7 - 1, 4 - 1) · 0.6^4 · 0.4^(7 - 4)
         = C(6, 3) · 0.6^4 · 0.4^3
         = 20 · 0.1296 · 0.064
         ≈ 0.1659.
```

In R (via the failures-before-success parameterization): `dnbinom(3, size = 4, prob = 0.6) ≈ 0.1659`. So the 4th hit on the 7th shot happens about **16.6%** of the time.

### Exercise 8 — answer

Let `A ~ Poisson(3)` and `B ~ Poisson(2)` be the customer counts at the two shops, independent. By the Poisson-convolution property (§2.9), `A + B ~ Poisson(3 + 2) = Poisson(5)`.

```
P(A + B = 5) = 5^5 · e^(-5) / 5!
             = 3125 · e^(-5) / 120
             ≈ 3125 · 0.006738 / 120
             ≈ 0.17547.
```

In R: `dpois(5, lambda = 5) ≈ 0.1755`. About **17.5%**. The "sum of independent Poissons is Poisson" result is doing the real work here — without it, we'd be summing a joint PMF over every `(a, b)` with `a + b = 5`.

### Exercise 9 — answer

`X` takes values `{0, 1, 2, 3, 4}` with PMF `(1/16, 4/16, 6/16, 4/16, 1/16)` — the familiar `(1, 4, 6, 4, 1)/16` Binomial(4, 0.5) shape.

Applying `Y = |X - 2|`:

| `X` | `Y = |X - 2|` | `P(X = x)` |
|---|---|---|
| 0 | 2 | 1/16 |
| 1 | 1 | 4/16 |
| 2 | 0 | 6/16 |
| 3 | 1 | 4/16 |
| 4 | 2 | 1/16 |

Collecting by `Y` (the pushforward from §2.10 — sum PMF values over preimages):

```
P(Y = 0) = P(X = 2) = 6/16 = 3/8.
P(Y = 1) = P(X = 1) + P(X = 3) = 4/16 + 4/16 = 8/16 = 1/2.
P(Y = 2) = P(X = 0) + P(X = 4) = 1/16 + 1/16 = 2/16 = 1/8.
```

Sanity: `3/8 + 1/2 + 1/8 = 3/8 + 4/8 + 1/8 = 8/8 = 1`. ✓

This is the pushforward from §2.10 in its simplest form — collect the preimages of each value of `Y` and sum.

### Exercise 10 — answer

Let `X` = prescriptions in a given hour. `X ~ Poisson(10)`.

**Part 1.** "Overload" means `X > 15`, i.e., `X ≥ 16`.

```
P(X > 15) = 1 - P(X ≤ 15) = 1 - ppois(15, 10) ≈ 0.04874.
```

So about **4.9%** per hour.

**Part 2.** Let `Y` = number of overloaded hours in the 8-hour day. Since hours are independent and each has the same overload probability `p ≈ 0.04874`, `Y ~ Binomial(8, p)`.

```
P(Y ≥ 1) = 1 - P(Y = 0) = 1 - (1 - p)^8
        ≈ 1 - 0.95126^8
        ≈ 1 - 0.6708
        ≈ 0.3292.
```

So about **33%** — roughly one workday in three has at least one overload hour. Intuitively: 8 independent shots at a 5% event give you a combined probability close to `8 · 5% = 40%` (the linear approximation via Bonferroni), and the exact Binomial tail is slightly less due to the overlap.

**Why this matters.** This is a **two-stage** problem — Poisson for within-hour variation, Binomial for aggregation across hours. Real-world capacity planning stacks distributions this way constantly (defect rates within-batch, batch-yield across-shifts, shift-failures across-weeks). Each layer gets its own distribution and its own independence assumption.
