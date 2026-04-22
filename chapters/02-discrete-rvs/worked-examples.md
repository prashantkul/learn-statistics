# Chapter 2 — Worked Examples

> Ten fully solved problems for *Discrete Random Variables and Their Distributions*. Difficulty ramps: warm-up (1–3), standard application (4–7), push (8–10). Every numerical answer is verified by `/tmp/smoke_ch2.R`.

---

## Example 1 (warm-up) — Indicator of an event is Bernoulli

Roll a fair six-sided die. Let `X = 1_{roll is 6}` be the indicator of the event "rolled a 6." What is the distribution of `X`?

**Solution.** The event `A = {roll is 6}` has `P(A) = 1/6` (Ch 1 §1.3). By the definition of an indicator, `X = 1_A ~ Bernoulli(P(A))`, so

```
X ~ Bernoulli(1/6),    P(X = 1) = 1/6,    P(X = 0) = 5/6.
```

**Why this matters.** Every event in Ch 1 gives rise to a Bernoulli RV via its indicator. This is the bridge from "events" (Ch 1) to "random variables" (Ch 2) — they're the same object through different lenses.

## Example 2 (warm-up) — Binomial direct computation

Flip a fair coin 5 times. What is the probability of exactly 3 heads?

**Solution.** Let `X` = number of heads. Each flip is an independent Bernoulli(0.5), so `X ~ Binomial(5, 0.5)`.

```
P(X = 3) = C(5, 3) · (1/2)^3 · (1/2)^2
         = 10 · 1/8 · 1/4
         = 10/32
         = 5/16 = 0.3125.
```

**R.** `dbinom(3, size = 5, prob = 0.5)` returns `0.3125`.

## Example 3 (warm-up) — Geometric (trial of first success)

Roll a fair die until a 6 appears. What is the probability that the first 6 comes on the 3rd roll?

**Solution.** Let `X` = trial number of the first 6. Under the "trial of first success" convention, `X ~ Geometric(1/6)`.

```
P(X = 3) = (5/6)^2 · (1/6)
         = 25/36 · 1/6
         = 25/216
         ≈ 0.1157.
```

The first two rolls must be non-6 (probability `5/6` each, by independence from Ch 1 §1.13), and the third must be a 6.

## Example 4 (standard) — Binomial tail probability

An A/B test exposes 200 visitors to the new landing page. Assume the conversion rate for this page is 5%. What is the probability you observe **at least 15 conversions**?

**Solution.** Let `X` = number of conversions. If conversions are independent across visitors with constant probability `p = 0.05`, `X ~ Binomial(200, 0.05)`.

```
P(X ≥ 15) = 1 - P(X ≤ 14) = 1 - F_X(14).
```

This is the Binomial CDF evaluated at 14. In R:

```r
1 - pbinom(14, size = 200, prob = 0.05)
```

which returns approximately **`0.0781`** — about a 7.8% chance. Intuitively: the expected count is `np = 10` with standard deviation `≈ √(np(1-p)) = √9.5 ≈ 3.08`, so 15 is about 1.6 SDs above the mean — moderately unusual, not rare.

**Commentary.** The assumption that conversions are independent across visitors is doing a lot of work. If two visitors are friends, they may influence each other's decision, violating independence. In practice analysts check against a Poisson or Negative Binomial fit to the actual distribution.

## Example 5 (standard) — Hypergeometric (quality control)

A shipment of 100 items contains 8 defective units. You inspect a random sample of 10 items **without replacement**. What is the probability that your sample contains **at least 2 defective units**?

**Solution.** Let `X` = number of defective units in your sample. With `N = 100`, `K = 8` defective, `n = 10` drawn, `X ~ Hypergeometric(100, 8, 10)`.

```
P(X ≥ 2) = 1 - P(X = 0) - P(X = 1).

             C(8, 0) · C(92, 10)
P(X = 0) = ─────────────────────
                  C(100, 10)

             C(8, 1) · C(92, 9)
P(X = 1) = ────────────────────
                  C(100, 10)
```

In R:

```r
1 - dhyper(0, m = 8, n = 92, k = 10) - dhyper(1, m = 8, n = 92, k = 10)
# ≈ 0.1820
```

So roughly an **18% chance** you'll catch 2 or more defects in your 10-item sample.

**Binomial approximation check.** If we (incorrectly) modeled this as Binomial(10, 0.08) — treating the draws as independent with constant probability — we'd get `1 - pbinom(1, 10, 0.08) ≈ 0.1879`. The two answers agree to one decimal because `n/N = 10/100 = 0.10` is modest; the finite population shrinks the tail from 18.8% to 18.2%. With `n/N = 0.5` the gap would be much larger.

## Example 6 (standard) — Poisson tail (web traffic)

A website averages 4 visitors per minute. Assume visits follow the Poisson paradigm. What is the probability of **8 or more visitors** in a given minute?

**Solution.** Let `X` = visitors in a minute. `X ~ Poisson(λ = 4)`.

```
P(X ≥ 8) = 1 - P(X ≤ 7) = 1 - ∑_{k=0}^{7} (4^k · e^(-4)) / k!.
```

In R: `1 - ppois(7, lambda = 4)` returns approximately **`0.0511`** — about 5.1%.

**Calibration.** In an 8-hour workday there are `8 × 60 = 480` one-minute windows. Expected number of minutes with ≥ 8 visitors: `480 × 0.0511 ≈ 24.5`. So spikes happen roughly every **20 minutes** just from Poisson fluctuation. Designing an alerting system around single spikes would fire all day. You'd instead alert on *runs* or *rate changes* — pretext for Ch 8.

## Example 7 (standard) — Negative Binomial

A coin has probability `p = 0.3` of heads. You flip until you've seen the **3rd heads**. What is the probability that the 3rd heads occurs on the **8th flip**?

**Solution.** Let `Y` = trial number of the 3rd heads. Then `Y ~ NegBinomial(r = 3, p = 0.3)`. Under our "trial of the r-th success" convention:

```
P(Y = 8) = C(8 - 1, 3 - 1) · p^3 · (1 - p)^(8 - 3)
         = C(7, 2) · 0.3^3 · 0.7^5
         = 21 · 0.027 · 0.16807
         ≈ 0.0953.
```

**R.** R's `dnbinom(k, size, prob)` parameterizes `k` as the number of **failures** before the r-th success. Our 8th-trial event = 3 successes + 5 failures, so:

```r
dnbinom(5, size = 3, prob = 0.3)
# 0.09529569
```

## Example 8 (push) — Binomial → Poisson approximation

A factory produces 1,000 widgets per day. Each widget is independently defective with probability `p = 0.003`. Let `X` = number of defective widgets in a day. Compute `P(X = 3)` exactly (Binomial) and compare to the Poisson approximation.

**Solution.** Exact: `X ~ Binomial(1000, 0.003)`.

```
P(X = 3) = C(1000, 3) · 0.003^3 · 0.997^997.
```

Computing `C(1000, 3) = 1000·999·998/6 = 166167000` and evaluating:

```r
dbinom(3, size = 1000, prob = 0.003)
# ≈ 0.2244
```

Poisson approximation: `np = 3`, so model `X ≈ Poisson(3)`.

```
P(X = 3) ≈ 3^3 · e^(-3) / 3! = 27 · e^(-3) / 6 = 4.5 · e^(-3) ≈ 0.2240.
```

```r
dpois(3, lambda = 3)
# ≈ 0.2240
```

The two numbers agree to three decimal places. The Poisson approximation is excellent here because `n = 1000` is large and `p = 0.003` is small with `np = 3` moderate — exactly the regime the Binomial → Poisson limit (§2.9) describes. For spreadsheet-scale computations where `C(1000, 3)` would overflow but Poisson is a one-line Taylor term, the approximation earns its keep.

## Example 9 (push) — Distribution of the sum of two dice

Roll two fair six-sided dice independently. Let `S = X + Y` be their sum. Derive the PMF of `S`, confirm it sums to 1, and compute `P(S = 7)`.

**Solution.** `X, Y ~ DUnif(1, 6)` and they're independent. The joint PMF is `P(X = i, Y = j) = 1/36` for every `(i, j) ∈ {1, ..., 6}²` (Ch 1 §1.13).

Then `P(S = s) = ∑_{i : 1 ≤ i ≤ 6, 1 ≤ s - i ≤ 6} P(X = i, Y = s - i) = (number of (i, j) pairs summing to s) / 36.` Counting:

| `s` | valid `(i, j)` | count | `P(S = s)` |
|---|---|---|---|
| 2 | (1,1) | 1 | 1/36 |
| 3 | (1,2), (2,1) | 2 | 2/36 |
| 4 | (1,3), (2,2), (3,1) | 3 | 3/36 |
| 5 | 4 pairs | 4 | 4/36 |
| 6 | 5 pairs | 5 | 5/36 |
| 7 | 6 pairs | 6 | **6/36 = 1/6** |
| 8 | 5 pairs | 5 | 5/36 |
| 9 | 4 pairs | 4 | 4/36 |
| 10 | 3 pairs | 3 | 3/36 |
| 11 | 2 pairs | 2 | 2/36 |
| 12 | (6,6) | 1 | 1/36 |

Sum of the count column: `1+2+3+4+5+6+5+4+3+2+1 = 36`. Total probability = 36/36 = 1. ✓

Answer: **`P(S = 7) = 6/36 = 1/6 ≈ 0.1667`**.

**Why this matters.** This is the first example of a **convolution**: the PMF of a sum of independent RVs is a convolution of their PMFs. The triangular shape — peak at the most-achievable sum, linearly decaying to the extremes — is a foreshadow of the CLT (Ch 5): sums of many independent uniforms approach a Gaussian shape. Two dice already show the bell starting to form.

## Example 10 (push) — Resolving the anchor, statistically

Back to the bank. Under the null model `X_t ~ Poisson(5)` of one-minute call volumes in a 9-hour shift (540 minutes), the supervisor reports seeing **three minutes with ≥ 12 calls** during this shift.

**(a)** What is the expected number of such spikes per shift if the null is true?

**(b)** If `Y` = number of ≥ 12-spike minutes in a 540-minute shift, what is `P(Y ≥ 3)` under the null?

**(c)** Is the supervisor's observation evidence against the null?

**Solution.**

**(a)** From §2.12, `p = P(X ≥ 12 | λ = 5) ≈ 0.005453`. Across 540 minutes:

```
E[Y] = 540 · p ≈ 540 · 0.005453 ≈ 2.944.
```

So even under the null, we expect **about 3 spikes per shift**.

**(b)** Each minute is an independent Bernoulli(`p`) trial for "is this a spike?" (Poisson(5) increments are independent across disjoint minutes), so `Y ~ Binomial(540, p)`. Because `p` is small and `n` is large, we can approximate `Y ≈ Poisson(540p) = Poisson(2.944)` (§2.9). Then

```
P(Y ≥ 3) = 1 - P(Y ≤ 2) ≈ 1 - ppois(2, 2.944) ≈ 1 - 0.4357 ≈ 0.5643.
```

So there's a **~56% chance** of seeing 3 or more spikes in a shift, purely from chance.

(Computing the exact Binomial gives essentially the same answer: `1 - pbinom(2, 540, 0.005453) ≈ 0.5649`.)

**(c)** **No.** Three spikes in a shift is roughly what you expect; `P(Y ≥ 3) ≈ 56%` is more common than not. The supervisor's observation is not evidence against the null — it's consistent with it. To actually conclude the baseline rate has shifted, we'd want to see something much more extreme (e.g., 7 spikes in a shift, which has `P(Y ≥ 7 | λ = 2.944) ≈ 0.031`) or to observe the spikes clustering in time (which would violate the Poisson-paradigm independence assumption, §2.13).

**The payoff.** The chapter's anchor is now completely resolved. The supervisor's escalation rests on two misreadings: (i) treating a single 12-call minute as rare when in fact it happens `≈ 3` times per shift, and (ii) treating *three spikes in a shift* as anomalous when that's the median outcome. Both errors are the same species — reading an individual tail probability (0.55%) as "unusual" without contextualizing it against the number of opportunities. This is a running theme in applied statistics, and it's what Ch 8 (hypothesis testing, p-values, multiple comparisons) is partly built to address.
