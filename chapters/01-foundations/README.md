# Chapter 1 — Foundations: Sample Spaces, Counting, Probability Axioms, Conditional Probability

> *"Probability is a measure on a sample space; everything else is bookkeeping."*
>
> 🎧 **Pre-built NotebookLM:** [open it here](https://notebooklm.google.com/notebook/bb199913-6520-4a21-95be-65c5dcd3dd5e) — audio overview (podcast), quiz, mind map, infographic, Briefing Doc + Study Guide reports, and chat-with-sources. Click *"Make a copy"* in NotebookLM to clone it to your own account.

## Anchor problem

> *"A recent audit found that 60% of positive COVID tests at City Hospital are false positives."* The test's sensitivity is 99% and specificity is 99%. How can such an accurate test be wrong 60% of the time?

Resolved in §1.12 using Bayes' rule and the base-rate / prevalence argument.

## Learning objectives

By the end of this chapter you will:

- Speak the language of **sample spaces** and **events** fluently (outcome, event, union, intersection, complement, disjoint, partition).
- State Kolmogorov's **three axioms of probability** and prove the basic consequences (complement rule, monotonicity, inclusion–exclusion).
- Count arrangements using the **multiplication rule**, **permutations**, **combinations**, and the overcounting / story-proof pattern.
- Compute **conditional probabilities** from first principles, use the **law of total probability** and the **multiplication rule**, and apply **Bayes' rule** to invert conditionals.
- Reason cleanly about **independence** — including why *pairwise* and *mutual* independence differ.
- Diagnose three classic base-rate pitfalls — **Monty Hall**, **Simpson's paradox**, and the **prosecutor's fallacy** — and explain why each is a conditioning error.
- Write short R programs that verify these probabilities via Monte Carlo simulation and exact arithmetic.

## Prerequisites

- Single-variable calculus (limits, derivatives, integrals).
- No multivariable calculus required yet.
- High-school statistics (mean, median, variance concept, reading a histogram, basic coin/die probability) — assumed, not re-taught.
- No linear algebra assumed.
- R installed with `tidyverse`, `ggplot2`, `gridExtra`, and the IRkernel registered with Jupyter. See [the repo's CONTRIBUTING.md](../../CONTRIBUTING.md) for a one-time setup.

## Estimated time

6–8 hours of focused reading, plus exercises.

## Sources at a glance

- **Blitzstein & Hwang** *Introduction to Probability*, Chs 1–2 (pp. 1–102). *Probability and counting* + *Conditional probability*.
- **Wasserman** *All of Statistics*, Ch 1 (pp. 3–18). *Probability*.

See `sources.md` for the per-section page map.

## What's in this folder

- `notes.md` — main tutorial (502 lines, 15 sections).
- `worked-examples.md` — 10 fully solved problems (difficulty-ramped).
- `exercises.md` — 10 practice problems with full answers at the bottom.
- `sources.md` — Blitzstein and Wasserman page references; external video links.
- `extracts.yaml` — page-range manifest for `scripts/extract_pages.py`.
- `code/01_foundations.ipynb` — R notebook (IRkernel: tidyverse + ggplot2 + Monte Carlo simulation). Executes cleanly; outputs and plots are saved in-file.
- `extracts/` (gitignored) — PDF excerpts of Blitzstein Chs 1–2 and Wasserman Ch 1, regenerated via `uv run scripts/extract_pages.py chapters/01-foundations`.
