# Chapter 2 — Discrete Random Variables and Their Distributions

> *"A random variable is a measurable function Ω → ℝ; its distribution is the pushforward."*
>
> 🎧 **Pre-built NotebookLM:** [open it here](https://notebooklm.google.com/notebook/38bf32c7-1249-406a-9a9a-71423130adad) — audio overview (podcast), quiz, mind map, slide deck, infographic, Briefing Doc + Study Guide reports, and chat-with-sources. Click *"Make a copy"* in NotebookLM to clone it to your own account.

## Anchor problem

> *"A bank's call center averages 5 calls/minute. At 9:03 AM, 12 calls arrive in one minute and the supervisor escalates — is this evidence of an anomaly, or is 12 calls/minute a perfectly ordinary Poisson fluctuation?"*

Resolved in §2.12 using the Poisson tail and calibrated against the number of one-minute windows in a shift.

## Learning objectives

By the end of this chapter you will:

- Define a **random variable** as a function `Ω → ℝ` and explain why `{X = x}` is a Ch 1 event in disguise.
- Write and plot the **PMF** and **CDF** of a discrete RV, and recover one from the other.
- Recognize the story for each of the six named discrete distributions (**Bernoulli**, **Binomial**, **Geometric**, **Negative Binomial**, **Hypergeometric**, **Poisson**) and derive each PMF from Ch 1 primitives (counting + independence + conditional probability).
- Derive the **Poisson as a limit of the Binomial** (`n → ∞`, `p → 0`, `np → λ`) — the one genuinely new piece of calculus in the chapter.
- Apply the **pushforward** rule to compute the PMF of a function `Y = g(X)`.
- Compute exact probabilities with R's built-ins: `dbinom`, `pbinom`, `rbinom`, `dgeom`, `dnbinom`, `dhyper`, `dpois`, `ppois`.
- Verify every closed-form answer with a Monte Carlo simulation — `rpois`, `rbinom`, `rgeom`, `rhyper`.

## Prerequisites

- Ch 1 (sample spaces, events, Kolmogorov axioms, counting, conditional probability, independence).
- Single-variable calculus (the only genuinely new piece of calc is the `(1 - λ/n)^n → e^(-λ)` limit in §2.9).
- No multivariable calculus required yet.
- No linear algebra assumed.
- R installed with `tidyverse`, `ggplot2`, `gridExtra`, and IRkernel registered with Jupyter. See [CONTRIBUTING.md](../../CONTRIBUTING.md) for setup.

## Estimated time

6–8 hours of focused reading, plus exercises.

## Sources at a glance

- **Blitzstein & Hwang** *Introduction to Probability*:
  - Ch 3 *Random variables and their distributions*, pp. 103–148 (whole chapter).
  - Ch 4 §4.3 *Geometric and Negative Binomial*, pp. 157–163.
  - Ch 4 §§4.7–4.8 *Poisson and its connection to Binomial*, pp. 174–183.
- **Wasserman** *All of Statistics*, Ch 2 §§2.1–2.3 *Random variables + discrete distributions*, pp. 19–26.

See `sources.md` for the section-by-section page map.

## What's in this folder

- `notes.md` — main tutorial (~600 lines, 15 sections).
- `worked-examples.md` — 10 fully solved problems (difficulty-ramped).
- `exercises.md` — 10 practice problems with full answers at the bottom.
- `sources.md` — Blitzstein and Wasserman page references; external video links.
- `extracts.yaml` — page-range manifest for `scripts/extract_pages.py`.
- `code/02_discrete_rvs.ipynb` — R notebook (IRkernel: tidyverse + ggplot2 + Monte Carlo). Executes cleanly; outputs and plots are saved in-file.
- `extracts/` (gitignored) — PDF excerpts of Blitzstein Ch 3, §4.3, §§4.7–4.8, and Wasserman §§2.1–2.3. Regenerate via `uv run scripts/extract_pages.py chapters/02-discrete-rvs`.
