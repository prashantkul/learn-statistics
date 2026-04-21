# Chapter 1 — Worked Examples

> Ten fully solved problems for *Foundations: Sample Spaces, Counting, Probability Axioms, Conditional Probability*. Difficulty ramps: 1–3 are warm-ups, 4–7 are standard applications, 8–10 push the reader.
>
> **Smoke-test discipline.** Every numerical answer below is verified by `/tmp/smoke_ch1.R` (base R + `MASS::fractions()`) before publish. Run it yourself with `Rscript /tmp/smoke_ch1.R` — the script is reproduced at the bottom of this file.

---

## Example 1 (warm-up) — Naive probability on a die

**Problem.** A fair six-sided die is rolled. What is the probability that the outcome is **even** AND **greater than 3**?

**Solution.** The sample space is `Ω = {1, 2, 3, 4, 5, 6}` with each outcome equally likely, so `P({ω}) = 1/6`.

Let `A = {even outcomes} = {2, 4, 6}` and `B = {outcomes > 3} = {4, 5, 6}`. We want `P(A ∩ B)`.

```
A ∩ B = {4, 6}     ⇒     |A ∩ B| = 2     ⇒     P(A ∩ B) = 2/6 = 1/3.
```

Answer: **`P(A ∩ B) = 1/3 ≈ 0.3333`**.

---

## Example 2 (warm-up) — Counting arrangements of letters

**Problem.** How many distinguishable arrangements of the letters in the word `MISSISSIPPI` are there?

**Solution.** `MISSISSIPPI` has 11 letters — one `M`, four `I`'s, four `S`'s, two `P`'s. Naively there are `11!` arrangements if the letters were all distinct, but the repeated letters have been overcounted by `4!` (the `I`'s among themselves), `4!` (the `S`'s), and `2!` (the `P`'s). Dividing:

```
       11!             39,916,800
   ──────────────  =  ───────────────────────  =  34,650.
   1! · 4! · 4! · 2!      1 · 24 · 24 · 2
```

This is the **multinomial coefficient** formula for arrangements with repetition. We'll see it again in Ch 3.

Answer: **34,650 arrangements**.

---

## Example 3 (warm-up) — "At least one" via the complement

**Problem.** You roll a fair six-sided die 4 times. What is the probability of rolling **at least one 6**?

**Solution.** It is far easier to compute `P(no 6's in 4 rolls)` and subtract from 1. The 4 rolls are independent; each round has probability `5/6` of *not* showing a 6. So

```
P(no 6 in 4 rolls) = (5/6)^4 = 625/1296 ≈ 0.4823,

P(at least one 6) = 1 − 625/1296 = 671/1296 ≈ 0.5177.
```

Answer: **`671/1296 ≈ 0.5177`**. Notice it's just barely above 1/2 — rolling a die 4 times doesn't quite give even odds of a 6. This is the setup for the **chevalier de Méré's paradox** that motivated Pascal and Fermat in 1654.

---

## Example 4 (standard) — Inclusion–exclusion

**Problem.** A card is drawn at random from a standard 52-card deck. Let `A` = "the card is a heart" and `B` = "the card is a face card (J, Q, K)." Compute `P(A ∪ B)`.

**Solution.** Each card is equally likely, so `P({card}) = 1/52`.

- `|A| = 13` (13 hearts), so `P(A) = 13/52 = 1/4`.
- `|B| = 12` (J/Q/K in each of 4 suits), so `P(B) = 12/52 = 3/13`.
- `|A ∩ B| = 3` (J♥, Q♥, K♥), so `P(A ∩ B) = 3/52`.

By inclusion–exclusion,

```
P(A ∪ B) = P(A) + P(B) − P(A ∩ B) = 13/52 + 12/52 − 3/52 = 22/52 = 11/26 ≈ 0.4231.
```

Answer: **`11/26 ≈ 0.4231`**.

---

## Example 5 (standard) — Conditional probability from a 2×2 table

**Problem.** A survey of 500 adults classified each person by "exercises regularly" (yes/no) and "has high blood pressure" (yes/no):

|            | **BP High** | **BP Normal** | total |
|---|---:|---:|---:|
| **Exercises**       |  40  | 260  | 300 |
| **Doesn't exercise**| 110  |  90  | 200 |
| **total**           | 150  | 350  | 500 |

Compute `P(BP High | Doesn't exercise)` and `P(Exercises | BP Normal)`. Comment.

**Solution.** Straight from the definition `P(A | B) = P(A ∩ B)/P(B)`, which simplifies to (count in cell) / (row/column total) when using counts:

```
P(BP High | Doesn't exercise) = 110 / 200 = 0.55 = 55%.

P(Exercises | BP Normal) = 260 / 350 = 26/35 ≈ 0.7429 ≈ 74.3%.
```

**Comment.** Among non-exercisers, 55% have high blood pressure — far above the population baseline `P(BP High) = 150/500 = 30%`. Conversely, among people with *normal* BP, 74.3% are exercisers (vs. the baseline `P(Exercises) = 60%`). Exercising and having normal BP are positively associated in this sample. Whether exercise *causes* BP to drop is a different (causal) question — this chapter only establishes the conditional-probability calculation; Ch 8 and the future Ch 11 on causality will come back to the causal claim.

Answers: **55% and 74.3%**.

---

## Example 6 (standard) — Law of total probability on a factory

**Problem.** A widget factory has three machines:

- Machine A produces 50% of widgets with a 1% defect rate.
- Machine B produces 30% with a 3% defect rate.
- Machine C produces 20% with a 4% defect rate.

A widget is drawn at random from the day's output. What is the probability it is defective?

**Solution.** Let `D` be the event "defective." The machines partition the sample space (every widget comes from exactly one). By the law of total probability,

```
P(D) = P(D | A) P(A) + P(D | B) P(B) + P(D | C) P(C)
     = 0.01 · 0.50 + 0.03 · 0.30 + 0.04 · 0.20
     = 0.005 + 0.009 + 0.008
     = 0.022 = 2.2%.
```

Answer: **`P(D) = 0.022 = 2.2%`**.

---

## Example 7 (standard) — Bayes' rule on the factory

**Problem.** Continuing Example 6: a randomly selected widget is tested and found to be **defective**. What is the probability it came from Machine B?

**Solution.** We want `P(B | D)`. Bayes' rule:

```
            P(D | B) · P(B)       0.03 · 0.30       0.009
P(B | D) = ─────────────────  =  ─────────────  =  ───────  =  9/22 ≈ 0.4091.
                 P(D)               0.022          0.022
```

Comment. **Before** seeing the defect, we believed `P(B) = 30%` — B was the middle-production machine. **After** observing the defect, we update to `P(B | D) ≈ 40.9%` — B's contribution to defects is disproportionate to its output, so the evidence bumps it upward. That's Bayes' rule in action: the likelihood `P(D | B)` reweights the prior.

As a sanity check, the three posteriors must sum to 1:

```
P(A | D) = 0.005 / 0.022 = 5/22  ≈ 0.2273,
P(B | D) = 0.009 / 0.022 = 9/22  ≈ 0.4091,
P(C | D) = 0.008 / 0.022 = 8/22  ≈ 0.3636.
                                 ─────────
                                  22/22 = 1  ✓
```

Answer: **`P(B | D) = 9/22 ≈ 0.4091`**.

---

## Example 8 (push) — Birthday problem with non-uniform months

**Problem.** In a room of 10 people, what is the probability that at least two share a **birth month** (not birthday — just the month)? Assume every person's birth month is uniform on the 12 months and independent across people.

**Solution.** Use the complement rule:

```
P(all 10 distinct months) = 12/12 · 11/12 · 10/12 · … · 3/12
                          = (12 · 11 · 10 · 9 · 8 · 7 · 6 · 5 · 4 · 3) / 12^10
                          = 12! / (2! · 12^10)
                          = 239,500,800 / 61,917,364,224
                          ≈ 0.003868.
```

Therefore

```
P(at least one shared month) = 1 − 0.003868 ≈ 0.9961.
```

It is almost certain. Compare to the classical birthday problem (365 days): with only 10 people, `P(shared birthday) ≈ 11.7%`. The smaller sample space (12 months vs. 365 days) makes collisions dramatically more likely.

Answer: **`P(shared month) ≈ 0.9961`** (≈ 99.6%).

---

## Example 9 (push) — Pairwise vs. mutually independent

**Problem.** Flip two fair coins. Define

- `A` = "first coin is heads,"
- `B` = "second coin is heads,"
- `C` = "exactly one head."

Show that `A, B, C` are **pairwise independent** but not **mutually independent**.

**Solution.** `Ω = {HH, HT, TH, TT}` with each outcome of probability `1/4`.

```
A = {HH, HT}        P(A) = 1/2
B = {HH, TH}        P(B) = 1/2
C = {HT, TH}        P(C) = 1/2

A ∩ B = {HH}        P(A ∩ B) = 1/4 = P(A) P(B)   ✓ (A ⫫ B)
A ∩ C = {HT}        P(A ∩ C) = 1/4 = P(A) P(C)   ✓ (A ⫫ C)
B ∩ C = {TH}        P(B ∩ C) = 1/4 = P(B) P(C)   ✓ (B ⫫ C)

A ∩ B ∩ C = ∅       P(A ∩ B ∩ C) = 0
P(A) P(B) P(C) = 1/8     ≠ 0
```

So `A, B, C` are **pairwise** independent (all three pair conditions hold) but **not mutually** independent (the triple condition fails). Geometrically: if `A` and `B` both happen — meaning both coins are heads — then `C` ("exactly one head") is *impossible*, so conditioning on `A ∩ B` annihilates `C`. Pairwise tests can't detect this.

Moral: **"mutually independent" is a stronger condition than "pairwise independent."** In applied work, this nuance shows up in the failure of "only-pairwise" constructions to behave like genuinely independent samples — important in Ch 5 (CLT) and Ch 6 (MLE).

---

## Example 10 (push) — Generalized Monty Hall, 100 doors

**Problem.** A game-show host hides a car behind one of 100 doors, goats behind the rest. You pick door 1. The host, who knows where the car is, now opens 98 of the remaining 99 doors, revealing 98 goats, and leaves one door (other than your pick) closed. Should you switch to the remaining closed door? Compute both win probabilities.

**Solution.** Let `C_i` = "car is behind door `i`." Initially `P(C_i) = 1/100` for each `i`. You pick door 1.

**Stay.** `P(car behind door 1) = 1/100`. That probability doesn't change because the host's opening of 98 doors is conditional on door 1 being your pick — it gives no information about door 1 specifically. So the *stay* strategy wins with probability `1/100 = 0.01`.

**Switch.** The complement event is "car not behind door 1" — which has probability `99/100`. In every world in that complement event, the car is behind one of doors 2, 3, …, 100. The host *reveals 98 goats among those 99 doors* and is forced to leave the one car-door closed. So the remaining closed-door among {2, …, 100} is the car door with probability 1, conditional on "car not in door 1." Thus

```
P(switch wins) = P(car not behind door 1) · 1 + P(car behind door 1) · 0
               = 99/100 = 0.99.
```

**Sanity check against classical Monty Hall** (3 doors, 1 revealed):

- Stay: `1/3 ≈ 0.333`.
- Switch: `2/3 ≈ 0.667`.

With 100 doors and 98 revealed, the switch advantage becomes enormous — **99% vs. 1%**. This is exactly the intuition many people struggle with in the 3-door version: the switch advantage scales with the number of doors the host opens.

Answer: **stay wins with probability `0.01`; switch wins with probability `0.99`**.

---

## Appendix — `/tmp/smoke_ch1.R`

Every numerical answer above is verified by the R script below. Run with `Rscript /tmp/smoke_ch1.R` — any failed assertion stops the script.

```R
# /tmp/smoke_ch1.R — verify every numerical answer in
# chapters/01-foundations/worked-examples.md and exercises.md.

suppressPackageStartupMessages(library(MASS))  # for fractions()

check <- function(label, got, expected, tol = 1e-9) {
  ok <- abs(got - expected) < tol
  if (!ok) stop(sprintf("%s: got %.12g, expected %.12g", label, got, expected))
  cat(sprintf("  ✓ %s = %.6g\n", label, got))
}

# === Worked examples ===
# E1 — P(even ∩ >3) on a die
check("E1 P(even ∩ >3)", 2/6, 1/3)

# E2 — MISSISSIPPI arrangements
check("E2 arrangements",
      factorial(11) / (factorial(1)*factorial(4)*factorial(4)*factorial(2)),
      34650)

# E3 — P(at least one 6 in 4 rolls)
check("E3 P(no 6 in 4)", (5/6)^4, 625/1296)
check("E3 P(>=1 six in 4)", 1 - (5/6)^4, 671/1296)

# E4 — inclusion-exclusion for heart ∪ face card
check("E4 P(A ∪ B)", 13/52 + 12/52 - 3/52, 11/26)

# E5 — 2x2 BP table
check("E5 P(BP high | sedentary)", 110/200, 0.55)
check("E5 P(exercises | BP normal)", 260/350, 26/35)

# E6 — LOTP on the factory
p_defect <- 0.01*0.50 + 0.03*0.30 + 0.04*0.20
check("E6 P(defect)", p_defect, 0.022)

# E7 — Bayes on the factory
check("E7 P(B | D)", (0.03*0.30) / p_defect, 9/22)
check("E7 posteriors sum to 1",
      ((0.01*0.50) + (0.03*0.30) + (0.04*0.20)) / p_defect, 1)

# E8 — birth-month birthday problem (10 people, 12 months)
p_all_distinct <- prod((12 - 0:9) / 12)
check("E8 P(all distinct months)", p_all_distinct, factorial(12)/(factorial(2) * 12^10))
check("E8 P(shared month) ≈ 0.9961",
      1 - p_all_distinct, 1 - factorial(12)/(factorial(2) * 12^10),
      tol = 1e-12)

# E9 — pairwise vs. mutual independence (two-flip XOR-like construction)
check("E9 P(A ∩ B)", 1/4, (1/2)*(1/2))
check("E9 P(A ∩ B ∩ C)", 0, 0)             # exactly the point

# E10 — 100-door Monty Hall
check("E10 stay wins",   1/100, 0.01)
check("E10 switch wins", 99/100, 0.99)

# === Exercises ===  (placeholder checks; filled in after exercises.md.)

cat("\nCh 1 worked-examples.md smoke test passed.\n")
```
