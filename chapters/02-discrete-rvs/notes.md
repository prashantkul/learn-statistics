# Chapter 2 — Discrete Random Variables and Their Distributions

> *"A random variable is a measurable function Ω → ℝ; its distribution is the pushforward."*

## §2.0 — The anchor problem

A busy commercial bank runs a call center that averages **5 customer calls per minute** across the business day. At 9:03 AM today, the floor supervisor watches the dashboard tick up to **12 calls in a single minute** and escalates: *"something is broken — a branch outage, a phishing alert, a news event — because we never see volume like this."*

Is the supervisor right? Or is 12-calls-in-a-minute a perfectly ordinary fluctuation we should expect to see a few times per shift just from random variation in call timing?

We can't answer this with Ch 1's machinery alone. Ch 1 taught us to compute `P(A)` for specific events — *the card is red*, *the test comes back positive* — but here we don't want `P` of one event; we want a **table** of probabilities: how likely is 0 calls, 1 call, 2 calls, …, 12 calls, 13 calls, … in a given minute? That table is what this chapter builds. We'll call it the **distribution** of a random variable `X` = "calls in a minute," and we'll discover that for the call-center setup it has a specific, named shape — the **Poisson** distribution — with a clean closed-form PMF that lets us compute `P(X ≥ 12 | λ = 5)` in one line of R. The answer is **≈ 0.55%** — rare, but in a 9-hour shift with 540 one-minute windows you should expect this spike **about 3 times per shift** just from chance. The supervisor's escalation is a false alarm.

We resolve this fully in §2.12. Everything between here and there is the machinery that gets us the answer.

## §2.1 — From sample spaces to random variables

In Ch 1 we built probability on a **sample space** `Ω` — the set of all possible outcomes — with events `A ⊆ Ω` and a probability measure `P: events → [0, 1]` satisfying Kolmogorov's three axioms (§1.6).

A **random variable** (RV) is a function from the sample space to the real numbers:

```
X : Ω → ℝ.
```

That's it. No new probability — a random variable is not itself random; it's a deterministic function that assigns a real number to each outcome `ω ∈ Ω`. The randomness lives in `ω`, not in `X`.

**Example.** Flip a coin twice. The sample space is `Ω = {HH, HT, TH, TT}`. Let `X` = "number of heads." Then

```
X(HH) = 2,   X(HT) = 1,   X(TH) = 1,   X(TT) = 0.
```

`X` is a function Ω → {0, 1, 2} ⊂ ℝ. Each outcome in `Ω` gets mapped to a real value by a fixed rule.

**Why bother?** Because we often care about a **summary number** of the outcome, not the raw outcome itself. In the coin example we probably care how many heads came up, not the specific sequence HT vs. TH. Defining `X` lets us compute probabilities of summary statements directly:

```
P(X = 1) = P({ω ∈ Ω : X(ω) = 1}) = P({HT, TH}) = 2/4 = 1/2.
```

The left side (`P(X = 1)`) is new notation. The right side is pure Ch 1: "probability of the event `{HT, TH}`." **The RV is just a convenient handle on a family of events of the form `{X = x}`.**

### Discrete vs. continuous — what this chapter covers

A random variable is **discrete** if its image — the set of values `X` can take — is finite or countably infinite (e.g., `{0, 1, 2, 3, ...}`). A fair die gives a discrete RV taking values in `{1, 2, 3, 4, 5, 6}`; the number of calls per minute is a discrete RV taking values in `{0, 1, 2, 3, ...}`.

This chapter is about discrete RVs. **Continuous RVs** — those taking values in an interval of ℝ, like a person's height — get their own chapter (Ch 4), because the PMF machinery below has to be replaced by a density (PDF) and sums by integrals. Everything in this chapter has a continuous analogue in Ch 4, and the analogies are clean.

### Notation convention

We use a capital letter (`X, Y, Z`) for the random variable itself and the matching lowercase letter (`x, y, z`) for a specific value it might take. `P(X = x)` means "the probability that `X` takes the value `x`." We write `X ∈ A` as shorthand for the event `{ω : X(ω) ∈ A}`. So `P(X ≥ 5)` means "the probability that `X(ω)` is at least 5," i.e., the probability of the event `{ω : X(ω) ≥ 5}`.

## §2.2 — PMF and CDF: two views of the same distribution

Given a discrete RV `X`, its **probability mass function** (PMF) is

```
p_X(x) = P(X = x).
```

The PMF tells you how much probability "mass" sits at each possible value of `X`. Three properties follow immediately from Kolmogorov's axioms (Ch 1 §1.6):

1. `p_X(x) ≥ 0` for every `x` (nonnegativity).
2. `p_X(x) = 0` for `x` outside the image of `X`.
3. `∑_x p_X(x) = 1`, where the sum runs over all `x` in the image (total probability).

These aren't new — they're consequences of the three axioms applied to the disjoint partition of `Ω` by the events `{X = x}`.

The **cumulative distribution function** (CDF) is

```
F_X(x) = P(X ≤ x) = ∑_{y ≤ x} p_X(y).
```

It's the running total of mass up to `x`. The CDF is defined for **every** real `x`, not just values in the image — `F_X(3.7)` makes sense even if `X` is integer-valued. Four properties follow:

1. `F_X` is nondecreasing.
2. `lim_{x → -∞} F_X(x) = 0`, `lim_{x → +∞} F_X(x) = 1`.
3. `F_X` is right-continuous (it has jumps at the image points, and at each jump the function equals the post-jump value).
4. `P(a < X ≤ b) = F_X(b) - F_X(a)`.

**The PMF and CDF carry the same information** — each can be recovered from the other. For a discrete RV supported on integers `0, 1, 2, ...`:

```
p_X(k) = F_X(k) - F_X(k - 1).
```

R names things consistently: `dbinom`, `dgeom`, `dpois` give PMFs; `pbinom`, `pgeom`, `ppois` give CDFs; the `d` is for "density/mass" and the `p` is for "probability (cumulative)."

**Example — two fair coins revisited.** For `X` = number of heads in two flips:

| `x` | `p_X(x)` | `F_X(x)` |
|---|---|---|
| 0 | 1/4 | 1/4 |
| 1 | 1/2 | 3/4 |
| 2 | 1/4 | 1 |

Sanity: the PMF sums to 1; the CDF ends at 1; between image points the CDF is flat (e.g., `F_X(1.5) = 3/4`).

### The distribution of `X`

We use "the distribution of `X`" to mean either the PMF or the CDF, interchangeably — they encode the same object. When a named distribution comes up (Bernoulli, Binomial, Poisson, …), the shorthand `X ~ Binomial(n, p)` means "`X` has the Binomial(n, p) distribution" — i.e., its PMF has the specific form below. The tilde is "is distributed as."

## §2.3 — Bernoulli: the atom of discrete probability

The simplest non-trivial random variable flips a coin:

```
X = 1 with probability p,
X = 0 with probability 1 - p.
```

We write `X ~ Bernoulli(p)` (or `Bern(p)` for short). `p` is called the **success probability**; `1 - p` is often called `q` by older texts.

**PMF:**

```
p_X(1) = p,
p_X(0) = 1 - p,
p_X(x) = 0 for x ∉ {0, 1}.
```

**Why "atom."** Every Ch-1 event `A ⊆ Ω` gives rise to a Bernoulli RV — its **indicator** `1_A`, defined by `1_A(ω) = 1` if `ω ∈ A` and `0` otherwise. Then `1_A ~ Bernoulli(P(A))`. Indicators are how we build every more complex discrete distribution below: Binomial is a sum of indicators, Geometric counts trials until the first indicator is 1, Poisson is a limit of sums of indicators. Ch 1's events and Ch 2's RVs are the same thing viewed through two lenses: an event is "is the indicator 1?", an RV is "what value did it take?"

### One-line aside: the point-mass (degenerate) distribution

A constant is a random variable. If `P(X = c) = 1` for some fixed real `c`, we say `X` has a **point mass at `c`**, written `X ~ δ_c`. It's a Bernoulli with `p = 1` (shifted to `c`). We'll rarely need it, but it's a useful corner case — a constant is not "not random"; it's a degenerate random variable whose PMF is concentrated at a single point.

## §2.4 — Binomial: sum of independent Bernoullis

Flip `n` coins, each with success probability `p`, independently. Let `X` = number of successes. Then

```
X ~ Binomial(n, p).
```

**PMF — derived, not cited.** The event `{X = k}` is the union of all length-`n` sequences with exactly `k` ones. The number of such sequences is `C(n, k) = n! / (k! (n-k)!)` — the **combinations** count from Ch 1 §1.5. Each specific sequence has probability `p^k (1-p)^{n-k}` by **independence** (Ch 1 §1.13): the `k` successes each contribute a factor of `p`, the `n-k` failures each contribute a factor of `1-p`, and because the trials are independent we multiply. Distinct sequences are disjoint events, so we add their probabilities:

```
P(X = k) = C(n, k) · p^k · (1 - p)^(n - k),     k = 0, 1, ..., n.
```

This is **Ch 1 in new clothes**: counting (§1.5) × independence (§1.13), nothing more. The Binomial PMF is not an axiom; it's a three-line derivation from Ch 1 primitives.

**Sanity checks.**

- `∑_{k=0}^n C(n,k) p^k (1-p)^{n-k} = (p + (1-p))^n = 1^n = 1` by the binomial theorem. Total probability sums to 1. ✓
- For `n = 1`, `P(X = 1) = p` and `P(X = 0) = 1 - p`. Binomial(1, p) = Bernoulli(p). ✓

**Plot intuition.** `p = 0.5, n = 10` is a symmetric bell around `k = 5`. `p = 0.1, n = 10` is right-skewed, concentrated near `k = 1`. As `n` grows with `p` fixed, the bell widens and becomes approximately Gaussian (foreshadow of Ch 5's CLT).

**R.** `dbinom(k, n, p)` is the PMF; `pbinom(k, n, p)` is the CDF `P(X ≤ k)`; `rbinom(m, n, p)` draws `m` samples.

```r
# P(X = 3) for X ~ Binomial(10, 0.3)
dbinom(3, size = 10, prob = 0.3)    # 0.2668

# P(X ≤ 3)
pbinom(3, size = 10, prob = 0.3)    # 0.6496

# 10 simulated draws
rbinom(10, size = 10, prob = 0.3)   # e.g., 2 4 3 3 5 3 2 4 3 1
```

## §2.5 — Independent random variables

Two RVs `X` and `Y` are **independent** if for every pair of values `x, y`:

```
P(X = x, Y = y) = P(X = x) · P(Y = y).
```

Equivalently, for every pair of sets `A, B ⊆ ℝ`:

```
P(X ∈ A, Y ∈ B) = P(X ∈ A) · P(Y ∈ B).
```

This is **independence of events** (Ch 1 §1.13), lifted to every pair of preimages. The event `{X = x}` and the event `{Y = y}` are independent in the Ch-1 sense, for every `(x, y)`. You can check independence of RVs by checking that their joint PMF factors into the product of their individual PMFs.

**Example.** In the Binomial derivation above, the `n` trials `X_1, X_2, ..., X_n` are independent Bernoulli(p) RVs; that's exactly how we got to multiply `p^k · (1-p)^{n-k}` over a specific sequence of outcomes.

**A warning — independence vs. conditional independence.** Same pitfall as Ch 1 §1.13: marginally independent doesn't imply conditionally independent, and vice versa. Two symptoms of a disease may be independent in the healthy population, dependent in the sick, and dependent marginally (Simpson's paradox territory — Ch 1 §1.14). We'll revisit this at length in Ch 4 (joint distributions).

**Sums of independent RVs.** If `X ~ Binomial(n_1, p)` and `Y ~ Binomial(n_2, p)` are independent (same success probability), then `X + Y ~ Binomial(n_1 + n_2, p)`. Intuition: `n_1` coins + `n_2` coins = `n_1 + n_2` coins, all same `p`, all independent. This convolution property shows up again for Poisson (§2.9) and several continuous families in Ch 4.

## §2.6 — Geometric and Negative Binomial: waiting times

The Binomial counts successes in a **fixed** number of trials. A natural dual: fix the number of successes and count the **trials required**.

### Geometric

Flip independent Bernoulli(p) coins until the **first success**. Let `X` = trial number on which the first success occurs. Then `X ~ Geometric(p)`.

**PMF — derived.** `{X = k}` happens iff the first `k-1` trials are failures and the `k`-th is a success. By independence:

```
P(X = k) = (1 - p)^(k - 1) · p,     k = 1, 2, 3, ...
```

Again Ch 1 §1.13 in new clothes. We already saw this in Ch 1 exercise 8 (first 6 on roll 4).

**Note on convention.** Some textbooks (including parts of Blitzstein and R's `dgeom`) define `X` = number of *failures before* the first success, so the support is `{0, 1, 2, ...}` with PMF `(1-p)^k · p`. Both conventions are standard; we use the "trial of first success" convention throughout this curriculum (support `{1, 2, 3, ...}`) because it matches everyday language. When calling R's `dgeom(k, p)`, translate: our `P(X = k)` = R's `dgeom(k - 1, p)`.

**Memoryless property.** For `X ~ Geometric(p)` and any positive integers `m, n`:

```
P(X > m + n | X > m) = P(X > n).
```

**Proof.**

```
P(X > m + n, X > m)     P(X > m + n)
P(X > m + n | X > m) = ────────────────────── = ──────────────
P(X > m)                P(X > m)

                      (1 - p)^(m + n)
                   = ────────────────── = (1 - p)^n = P(X > n). ✓
                       (1 - p)^m
```

(Using `P(X > k) = (1-p)^k`, which is the probability of `k` failures in a row.) This is the discrete analogue of the exponential's memorylessness (Ch 4). The practical implication: the coin has no memory of past flips, so "I'm overdue for a heads" is a fallacy — a favorite topic of Ch 1's pitfalls.

### Negative Binomial

Now fix a target number of successes `r` and count the trial number on which the `r`-th success occurs. Let `Y` = trial of the `r`-th success; then `Y ~ NegBinomial(r, p)`.

**PMF — derived.** `{Y = k}` happens iff among the first `k-1` trials there are exactly `r-1` successes, **and** the `k`-th trial is a success. The first part is a Binomial-like count with `C(k-1, r-1)` ways, each with probability `p^(r-1) (1-p)^(k-r)`; the `k`-th trial contributes another `p`. So

```
P(Y = k) = C(k - 1, r - 1) · p^r · (1 - p)^(k - r),     k = r, r+1, r+2, ...
```

Sanity: `r = 1` gives the Geometric PMF. ✓

(R uses a different parameterization — `dnbinom(k, size = r, prob = p)` gives the PMF of the *failures before* the `r`-th success. See the R notebook for the translation.)

## §2.7 — Hypergeometric: sampling without replacement

An urn holds `N` balls, `K` of them white and `N - K` of them black. Draw `n` balls **without replacement**. Let `X` = number of white balls drawn. Then `X ~ Hypergeometric(N, K, n)`.

**PMF — derived.** `{X = k}` happens iff we draw exactly `k` whites from the `K` available and exactly `n - k` blacks from the `N - K` available. By Ch 1's naive probability (§1.3) with counting (§1.5):

```
               C(K, k) · C(N - K, n - k)
P(X = k) = ─────────────────────────────────,     max(0, n - (N-K)) ≤ k ≤ min(n, K).
                     C(N, n)
```

Numerator: number of ways to pick `k` whites times `n-k` blacks. Denominator: total number of ways to pick `n` balls from `N`. It's Ch 1's counting and Ch 1's "conditional probability via sampling-without-replacement" machinery (§1.9), fully inside the naive-probability frame.

**Binomial vs. Hypergeometric — when does it matter?**

- **With replacement:** each draw has `P(white) = K/N` independently → `X ~ Binomial(n, K/N)`.
- **Without replacement:** draws are dependent (drawing a white reduces the chance the next is white) → `X ~ Hypergeometric(N, K, n)`.

When `N` is much larger than `n` (rule of thumb: `n/N < 0.05`) the two PMFs are nearly indistinguishable — drawing 10 cards from a 10,000-card deck *approximately* resembles drawing with replacement, so the Binomial is a fine approximation. When `n` is a substantial fraction of `N`, the Hypergeometric shrinks the tails relative to Binomial because the finite population enforces balance.

**R.** `dhyper(k, m, n_black, k_drawn)` where `m` = number of white balls, `n_black` = number of black balls, `k_drawn` = sample size.

## §2.8 — Discrete Uniform

Take a finite set `{a, a+1, ..., b}` and put equal mass on each value: `P(X = k) = 1/(b - a + 1)` for `k ∈ {a, ..., b}`. That's `X ~ DUnif(a, b)`. A fair die is `DUnif(1, 6)`. A fair coin (labeled 0/1) is `DUnif(0, 1) = Bernoulli(1/2)`. Uninteresting on its own, but shows up as a baseline in inference (max-entropy prior when you have no information, Ch 10) and as a component in constructions like the Monty Hall simulation.

## §2.9 — Poisson: limit of Binomial

The **Poisson** is the distribution of "count of rare events in a fixed exposure." Examples: radioactive decays per second, typos per page, customer calls per minute, genetic mutations per genome.

**Definition.** `X ~ Poisson(λ)` has PMF

```
P(X = k) = (λ^k · e^(-λ)) / k!,     k = 0, 1, 2, 3, ...
```

for a parameter `λ > 0`. Sanity: `∑_{k=0}^∞ λ^k / k! = e^λ` (Taylor series of the exponential), so the PMF sums to `e^(-λ) · e^λ = 1`. ✓

**Derivation: Poisson as a Binomial limit.** Fix `λ > 0` and imagine a Binomial with `n` trials and per-trial success probability `p = λ/n`. The expected number of successes is `n · p = λ`, regardless of `n`. As `n → ∞` the trials become more numerous but each individual trial becomes vanishingly unlikely, while the total expected count stays at `λ`. We claim the Binomial PMF converges to the Poisson PMF in this limit.

Start from the Binomial PMF at a fixed `k`:

```
P(X_n = k) = C(n, k) · (λ/n)^k · (1 - λ/n)^(n - k)

           = [n! / (k! (n-k)!)] · λ^k / n^k · (1 - λ/n)^n · (1 - λ/n)^(-k)
```

Group the three `n`-dependent factors and take `n → ∞`:

1. `n! / ((n-k)! · n^k) = [n · (n-1) · ... · (n-k+1)] / n^k → 1` as `n → ∞` (each of the `k` factors tends to 1).
2. `(1 - λ/n)^n → e^(-λ)` (calculus limit definition of `e`).
3. `(1 - λ/n)^(-k) → 1` as `n → ∞`.

Putting it together:

```
P(X_n = k) → (λ^k / k!) · e^(-λ) = Poisson(λ) PMF.   ✓
```

This is the **only genuinely new piece of calculus in the chapter** — the rest has been combinatorics + Ch 1 axioms. The Poisson is what you get when you model "rare events in a fixed exposure" as the limit of many tiny independent Bernoulli trials. `λ` is the **expected** number of events (we'll define expectation rigorously in Ch 3; for now treat it as the long-run average).

**Calibration for the anchor.** Our call center gets 5 calls/minute on average. To model "calls in a given minute," imagine dividing the minute into `n` sub-intervals, each with a tiny `p = 5/n` probability of a call, independently across sub-intervals. As `n → ∞` that's Poisson(5). The approximations (constant rate, independence, no two calls at the exact same instant) are the **Poisson paradigm** assumptions — see §2.13.

**Sum of independent Poissons.** If `X ~ Poisson(λ_1)` and `Y ~ Poisson(λ_2)` are independent, then `X + Y ~ Poisson(λ_1 + λ_2)`. Combining the 9 AM and 10 AM one-minute windows (each Poisson(5) and independent) gives a Poisson(10) count for the two minutes combined.

**R.** `dpois(k, lambda)` is the PMF; `ppois(k, lambda)` is the CDF; `rpois(n, lambda)` samples.

```r
# P(X = 12) for X ~ Poisson(5)
dpois(12, lambda = 5)           # 0.003425
# P(X ≥ 12) = 1 - P(X ≤ 11)
1 - ppois(11, lambda = 5)       # 0.005453
```

## §2.10 — Functions of a discrete random variable

If `X` is a discrete RV and `g: ℝ → ℝ` is any function, then `Y = g(X)` is also a discrete RV. Its PMF is obtained by the **pushforward**:

```
P(Y = y) = P(g(X) = y) = P({x : g(x) = y}) = ∑_{x : g(x) = y} p_X(x).
```

In English: to find the probability that `Y` equals `y`, collect every value `x` that `g` maps to `y` and sum their PMF values.

**Example.** Roll a fair die. `X ~ DUnif(1, 6)` and `Y = X mod 2` (1 if odd, 0 if even).

```
P(Y = 0) = P(X ∈ {2, 4, 6}) = 3/6 = 1/2.
P(Y = 1) = P(X ∈ {1, 3, 5}) = 3/6 = 1/2.
```

So `Y ~ Bernoulli(1/2)`. This is pure Ch 1: the event `{Y = y}` is the union of the disjoint events `{X = x}` over every `x` with `g(x) = y`, and we add their probabilities by the Ch 1 axioms.

**Why this matters.** Every **statistic** you'll compute in later chapters — the sample mean, the sample variance, a Chi-squared test statistic — is a function of random data. Understanding how a function of RVs gets its distribution is the mechanism behind every sampling distribution you'll meet. In Ch 4 we'll do the continuous version (change of variable with a Jacobian); here the discrete version is just "collect and sum."

## §2.11 — Joint, marginal, conditional PMFs (preview)

Full treatment in Ch 4. The short story for the discrete case:

- **Joint PMF.** For two discrete RVs `X, Y`, `p_{X,Y}(x, y) = P(X = x, Y = y)`. Nonnegative, sums to 1 over all `(x, y)`.
- **Marginal PMF.** Sum the joint over the other variable: `p_X(x) = ∑_y p_{X,Y}(x, y)`. This is the **Law of Total Probability** (Ch 1 §1.10) with `{Y = y}` as the partition.
- **Conditional PMF.** `p_{X | Y}(x | y) = P(X = x | Y = y) = p_{X,Y}(x, y) / p_Y(y)`, valid when `p_Y(y) > 0`. This is Ch 1's definition of conditional probability (§1.9), applied to the events `{X = x}` and `{Y = y}`.

Conditioning, LOTP, Bayes — all machinery from Ch 1 — carry over directly. The "new" thing is just tabulating probabilities over a 2-D grid instead of a 1-D list.

## §2.12 — Resolving the anchor: is 12 calls/minute weird?

Back to the bank. We model `X` = calls in a given minute as `Poisson(λ = 5)`. We want `P(X ≥ 12)`.

```
P(X ≥ 12) = 1 - P(X ≤ 11) = 1 - ∑_{k=0}^{11} (5^k · e^(-5)) / k!

         ≈ 1 - 0.9945
         ≈ 0.005453.
```

One in every `1 / 0.005453 ≈ 183` minutes. In a 9-hour shift with `9 · 60 = 540` one-minute windows, the **expected** number of spikes ≥ 12 is `540 · 0.005453 ≈ 2.9` — roughly **3 spikes per shift just from chance**, no news event required. The escalation is a false alarm.

```r
# The punchline in one line of R
1 - ppois(11, lambda = 5)   # 0.005453

# Expected spikes per shift
540 * (1 - ppois(11, lambda = 5))   # 2.944
```

**But wait — shouldn't the supervisor investigate anyway?** The **right** question is: is 12 calls/minute *much more common than 0.55% today* compared to a baseline? That's a **hypothesis test** — the subject of Ch 8. What Ch 2 tells us is the **null distribution** under "things are normal": if the Poisson(5) model holds, spikes of this size are rare-but-expected. Whether the *rate* of spikes has changed (e.g., "3 spikes in the last 10 minutes" rather than 3 in 540) is the statistically informative signal, not any single spike in isolation. This is the setup for sequential testing, change-point detection, and alerting — real applied probability.

## §2.13 — Pitfalls

### Pitfall 1 — "Poisson" requires the three paradigm assumptions

The Poisson model assumes (i) a **constant rate** `λ` across the exposure window, (ii) **independence** of disjoint sub-intervals, and (iii) **no simultaneity** (at most one event per instant). Violations:

- **Time-of-day variation.** Call-center volume at 9 AM vs. 3 AM is not constant. Model per-hour, not per-day.
- **Clustering.** If a news event triggers calls, one call increases the chance of the next — trials are no longer independent. The empirical distribution will have fatter tails than Poisson.
- **Batched events.** If the phone system groups incoming calls into 1-minute batches released together, multiple "events" land at the same instant. The Poisson undercounts simultaneity, so variance will be higher than `λ`.

The **Poisson paradigm** (Blitzstein §4.7) says: approximately, if (i)–(iii) hold approximately, the Poisson is a good approximation. But always check that the generating mechanism isn't violating the assumptions wholesale.

### Pitfall 2 — With vs. without replacement

A classic undergraduate trap. *"I pick 5 cards from a 52-card deck — what's the chance I get exactly 2 aces?"* If you compute `C(5,2) · (4/52)^2 · (48/52)^3`, you've used the Binomial (with replacement) when the problem is Hypergeometric (without replacement). The correct computation:

```
C(4, 2) · C(48, 3)        6 · 17296       103776
────────────────────  =  ─────────────  =  ────────  ≈ 0.0399.
     C(52, 5)              2598960         2598960
```

The Binomial approximation gives `≈ 0.0399` too, coincidentally close here because `n/N = 5/52 < 0.1`. With larger `n/N`, the gap widens.

### Pitfall 3 — Confusing `P(X = x)` with `p_X(x)` — and distribution name with RV

Students new to the notation sometimes write `P(X) = 0.3` or `Binomial = 5`. The grammar is:

- `X ~ Binomial(10, 0.3)` — `X` is a random variable; its distribution is Binomial(10, 0.3).
- `P(X = 5) = dbinom(5, 10, 0.3)` — a probability, a number in `[0, 1]`.
- `p_X(x) = P(X = x)` — a function of `x`, evaluated at a specific `x`.

"What's the Binomial?" is not a well-posed question; "what's `P(X = 5)` for `X ~ Binomial(10, 0.3)`?" is.

### Pitfall 4 — Support confusion

Each distribution has a specific **support** (set of values where the PMF is nonzero):

- Bernoulli: `{0, 1}`.
- Binomial(n, p): `{0, 1, ..., n}`.
- Geometric (our convention): `{1, 2, 3, ...}`.
- NegBinomial(r, p): `{r, r+1, r+2, ...}`.
- Hypergeometric: `{max(0, n-(N-K)), ..., min(n, K)}` (nontrivial — be careful).
- Poisson: `{0, 1, 2, 3, ...}`.

R's built-in functions return 0 for `x` outside the support; they don't complain. Always check the support before interpreting a probability.

## §2.14 — Summary and bridge to Ch 3

### What you now own

- An RV is a function `Ω → ℝ`; the event `{X = x}` is just a Ch-1 event re-indexed by `x`.
- A PMF `p_X(x) = P(X = x)` and a CDF `F_X(x) = P(X ≤ x)` are two equivalent views of the same distribution.
- The six named discrete distributions below, each with a story and a derived PMF.

### Summary table — the six named discrete distributions

| Distribution | Story | PMF | Support | Mean (Ch 3) | Variance (Ch 3) |
|---|---|---|---|---|---|
| `Bernoulli(p)` | One trial, success w.p. `p` | `p^x (1-p)^{1-x}` | `{0, 1}` | `p` | `p(1-p)` |
| `Binomial(n, p)` | `n` independent Bernoullis, count successes | `C(n,k) p^k (1-p)^{n-k}` | `{0, ..., n}` | `np` | `np(1-p)` |
| `Geometric(p)` (our conv.) | Trial of first success | `(1-p)^{k-1} p` | `{1, 2, ...}` | `1/p` | `(1-p)/p²` |
| `NegBinomial(r, p)` | Trial of `r`-th success | `C(k-1, r-1) p^r (1-p)^{k-r}` | `{r, r+1, ...}` | `r/p` | `r(1-p)/p²` |
| `Hypergeometric(N, K, n)` | `n` draws without replacement, count whites | `C(K,k) C(N-K, n-k) / C(N,n)` | range depends on params | `nK/N` | `nK(N-K)(N-n) / (N²(N-1))` |
| `Poisson(λ)` | Count of rare events at rate `λ` in a fixed window | `λ^k e^{-λ} / k!` | `{0, 1, 2, ...}` | `λ` | `λ` |

The **Mean** and **Variance** columns are forward references — we derive them all in Ch 3, where expectation gets its own chapter.

### Forward references to related distributions we don't cover here

- **Multinomial.** Generalizes Binomial from 2 outcomes to `k` outcomes per trial. The right chapter is Ch 4 (joint distributions), because its natural home is a multi-dimensional PMF.
- **Beta-Binomial.** A Binomial whose `p` is itself drawn from a Beta distribution — a compound. Covered in Ch 10 (Bayesian inference, conjugate priors), where it comes up naturally.
- **Poisson process.** The time-domain companion of Poisson: counts are Poisson, inter-arrival times are Exponential (continuous, Ch 4). Foreshadowed in Ch 5 when we discuss limit theorems for sequential data.

### Bridge to Ch 3

We now have distributions — shapes. Ch 3 compresses each shape to a few summary numbers: the **expectation** `E[X]` (the long-run average), the **variance** `Var(X)` (how spread out the mass is), and the **covariance** `Cov(X, Y)` (how two RVs co-vary). These summaries don't replace the distribution — they condense it. Many questions about a distribution have clean expectation-only answers (*how long until I see a 6 on average? 6 rolls. Why? `E[Geometric(1/6)] = 6`.*), and that's the payoff of Ch 3's linearity-of-expectation machinery.

Everything we proved here — the PMF derivations, the Binomial → Poisson limit, the sum-of-independents convolutions — is still load-bearing for Ch 3. Chapter 3 doesn't replace Chapter 2; it summarizes it.
