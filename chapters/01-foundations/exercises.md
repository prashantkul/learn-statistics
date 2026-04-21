# Chapter 1 — Exercises

> Ten practice problems for *Foundations: Sample Spaces, Counting, Probability Axioms, Conditional Probability*. Difficulty ramps: warm-up (1–3), standard application (4–7), push-yourself (8–10). Full answers are at the bottom.
>
> **Smoke-test discipline.** Every numerical answer below is verified by `/tmp/smoke_ch1.R` before publish. Try the exercises first — if your number disagrees with the answer key, run the smoke test to see whose arithmetic is right.

---

## Problems

### Exercise 1 (warm-up)

A fair six-sided die is rolled once. Compute `P(A ∪ B)` where `A = {prime outcomes}` and `B = {outcomes ≥ 5}`.

### Exercise 2 (warm-up)

A committee of 5 people must be chosen from a pool of 8 women and 4 men. How many **committees of exactly 3 women and 2 men** are possible?

### Exercise 3 (warm-up)

You flip a fair coin 10 times. What is the probability that **at least one flip is tails**?

### Exercise 4 (standard)

A standard 52-card deck is shuffled. A single card is drawn. Let `A` = "the card is red" (hearts or diamonds) and `B` = "the card is a King." Are `A` and `B` independent? Prove it by checking the definition.

### Exercise 5 (standard)

Of the students at a university, 60% own a laptop, 25% own a tablet, and 15% own both. A student is chosen at random.

1. What is `P(laptop ∪ tablet)` — the probability they own at least one device?
2. What is `P(laptop | tablet)` — the probability they own a laptop, given they own a tablet?
3. Are "owns a laptop" and "owns a tablet" independent events in this population?

### Exercise 6 (standard)

An urn contains 4 white, 6 black, and 5 red balls. Two balls are drawn **without replacement**. What is the probability that the first is white **and** the second is black?

### Exercise 7 (standard)

A medical screening test has 92% sensitivity and 85% specificity. In a population with prevalence 2%, a randomly chosen person tests positive. What is the probability they actually have the disease? Round to the nearest percent.

### Exercise 8 (push)

A fair die is rolled until a 6 appears. What is the probability that the first 6 appears on **the 4th roll**? (This is a special case of the **geometric distribution**, which we'll formalize in Ch 2.)

### Exercise 9 (push)

You have three boxes. Box 1 contains 2 gold coins, Box 2 contains 1 gold and 1 silver, Box 3 contains 2 silver coins. You pick a box uniformly at random, then draw a coin uniformly at random from it. The coin is **gold**. What is the probability the coin came from Box 1?

(This is **Bertrand's box paradox**. The textbook-classic "wrong answer" is 1/2 by symmetry. The correct answer is different.)

### Exercise 10 (push)

Two different medical tests, `T_A` and `T_B`, are available for the same disease. You know:

- Disease prevalence: `P(D+) = 0.01`.
- `T_A`: sensitivity `P(T_A+ | D+) = 0.95`, specificity `P(T_A− | D−) = 0.97`.
- `T_B`: sensitivity `P(T_B+ | D+) = 0.90`, specificity `P(T_B− | D−) = 0.99`.
- **Given the disease status, the two tests are conditionally independent.**

You take both tests. Both come back **positive**. What is `P(D+ | T_A+ ∩ T_B+)`?

This is your first encounter with combining multiple pieces of evidence in Bayes' rule — a warm-up for posterior updating in Ch 10.

---

## Answers

### Exercise 1 — answer

`Ω = {1, 2, 3, 4, 5, 6}`. Primes in `Ω` are `{2, 3, 5}`, so `A = {2, 3, 5}`, `P(A) = 3/6 = 1/2`. `B = {5, 6}`, `P(B) = 2/6 = 1/3`. `A ∩ B = {5}`, `P(A ∩ B) = 1/6`.

By inclusion–exclusion,

```
P(A ∪ B) = 1/2 + 1/3 − 1/6 = 3/6 + 2/6 − 1/6 = 4/6 = 2/3 ≈ 0.6667.
```

Answer: **`P(A ∪ B) = 2/3`**.

### Exercise 2 — answer

`choose(8, 3) · choose(4, 2) = 56 · 6 = 336`.

Answer: **336 committees**.

### Exercise 3 — answer

Easier via the complement: `P(all heads) = (1/2)^10 = 1/1024`. So

```
P(at least one tails) = 1 − 1/1024 = 1023/1024 ≈ 0.99902.
```

Answer: **`1023/1024 ≈ 0.9990`**.

### Exercise 4 — answer

```
P(A) = 26/52 = 1/2,
P(B) = 4/52  = 1/13,
P(A ∩ B) = 2/52 = 1/26    (red Kings: K♥, K♦).

P(A) · P(B) = (1/2)(1/13) = 1/26 = P(A ∩ B).   ✓
```

So **yes**, `A` and `B` are independent. Intuitively this reflects the deck's symmetric construction — suits and ranks are independent features.

### Exercise 5 — answer

Let `L` = "owns laptop," `T` = "owns tablet." Given: `P(L) = 0.60`, `P(T) = 0.25`, `P(L ∩ T) = 0.15`.

1. `P(L ∪ T) = P(L) + P(T) − P(L ∩ T) = 0.60 + 0.25 − 0.15 = 0.70`. **70%**.
2. `P(L | T) = P(L ∩ T) / P(T) = 0.15 / 0.25 = 0.60`. **60%**.
3. `L` and `T` are **independent** iff `P(L ∩ T) = P(L) · P(T)`. Here `P(L) · P(T) = 0.60 · 0.25 = 0.15 = P(L ∩ T)`. ✓ So yes, independent.
   Equivalently, part (2) revealed `P(L | T) = P(L)` — conditioning on tablet ownership didn't change the probability of laptop ownership. That's the definition of independence.

### Exercise 6 — answer

Let `W_1` = "first ball white," `B_2` = "second ball black."

```
P(W_1 ∩ B_2) = P(W_1) · P(B_2 | W_1)
             = (4/15) · (6/14)
             = 24/210
             = 4/35 ≈ 0.1143.
```

Answer: **`4/35 ≈ 0.1143`**.

### Exercise 7 — answer

Bayes' rule:

```
                  P(T+ | D+) P(D+)
P(D+ | T+)  =  ─────────────────────────────────────────────
               P(T+ | D+) P(D+) + P(T+ | D−) P(D−)

                   0.92 · 0.02                        0.0184
           =  ─────────────────────────────────  =  ─────────  ≈ 0.1113.
              0.92 · 0.02 + 0.15 · 0.98             0.165400
```

So despite a positive test, the probability they actually have the disease is only about **11%** — another base-rate illustration: low prevalence turns even a fairly good test into a mostly-false-positive generator.

### Exercise 8 — answer

For the first 6 to appear on the 4th roll, we need the first three rolls to **not** be 6's and the 4th roll to be a 6. The rolls are independent, so

```
P(first 6 on roll 4) = (5/6)^3 · (1/6)
                     = 125/216 · 1/6
                     = 125/1296
                     ≈ 0.09645.
```

Answer: **`125/1296 ≈ 0.0964`**. In Ch 2 we'll call this `P(X = 4)` where `X ~ Geometric(1/6)`.

### Exercise 9 — answer (Bertrand's box)

Let `B_1, B_2, B_3` be the three boxes (uniform prior `1/3` each). Let `G` = "drew a gold coin."

```
P(G | B_1) = 1     (both coins gold)
P(G | B_2) = 1/2   (one gold, one silver)
P(G | B_3) = 0     (no gold coins)
```

By the law of total probability,

```
P(G) = 1 · 1/3 + 1/2 · 1/3 + 0 · 1/3 = 1/3 + 1/6 = 1/2.
```

By Bayes' rule,

```
                P(G | B_1) · P(B_1)       1 · 1/3       1/3
P(B_1 | G)  =  ──────────────────────  =  ──────────  =  ─────  =  2/3.
                        P(G)                 1/2          1/2
```

Answer: **`P(B_1 | G) = 2/3 ≈ 0.6667`**, not 1/2.

Why isn't it 1/2? Intuition: of the three gold coins in the system (two in Box 1, one in Box 2), two of them live in Box 1. Given that you drew a gold coin, by symmetry among gold coins, you are 2/3 likely to have drawn one of Box 1's golds. The "symmetry by box" reasoning (1/2) ignores that Box 1 has twice as many chances of producing a gold draw as Box 2.

### Exercise 10 — answer

Let `E = T_A+ ∩ T_B+`. By conditional independence,

```
P(E | D+) = 0.95 · 0.90 = 0.855,
P(E | D−) = (1 − 0.97) · (1 − 0.99) = 0.03 · 0.01 = 0.0003.
```

By the law of total probability,

```
P(E) = P(E | D+) P(D+) + P(E | D−) P(D−)
     = 0.855 · 0.01 + 0.0003 · 0.99
     = 0.00855 + 0.000297
     = 0.008847.
```

By Bayes' rule,

```
               P(E | D+) P(D+)      0.00855
P(D+ | E)  =  ───────────────────  =  ─────────  ≈ 0.9664.
                   P(E)              0.008847
```

Answer: **about 96.6%**.

Compare to Exercise 7's 11% posterior from a single positive. Observing **two** independent positives is dramatic evidence of disease — the posterior rockets from the 1% prior to ≈97%. This is the multiplicative kick of combining independent likelihoods, and it's the core mechanism behind sequential testing, diagnostic pipelines, and Bayesian updating in general.
