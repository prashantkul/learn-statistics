# "Stats from First Principles" — Curriculum Plan

A 10-chapter self-study curriculum on probability and statistics, built in the same format as the companion repo at `~/Documents/source-code/learn-linear-algebra`. This document is the handoff spec: a fresh Claude Code instance should be able to read this and start Ch 1.

---

## 1. Goals and audience

**Audience.** Readers comfortable with **calculus** (single- and multivariable): limits, derivatives, integrals, partial derivatives, basic multi-integrals, and a touch of optimization (setting a gradient to zero). Algebra, summation notation, basic set theory, and **high-school statistics** (mean, median, variance concept, reading a histogram, fair-coin/fair-die probability) are assumed.

**Linear algebra is not a hard prerequisite.** Matrices are introduced just-in-time as bookkeeping — defined on the page where they first appear (expectation/covariance as a vector/matrix in Ch 3, multivariate normal in Ch 4). **Ch 9 (linear regression)** is the one chapter that benefits meaningfully from LA: readers who've done the sister [`learn-linear-algebra`](~/Documents/source-code/learn-linear-algebra) curriculum will recognize the normal equations as projection onto the column space (Ch 7 of LA) and gain geometric intuition; readers without LA will still follow the algebra and get the same working formulas. Ch 9's `notes.md` should have an explicit "**LA optional sidebar**" rendering the geometric picture, cleanly separable from the main algebraic derivation.

**Non-goals.** This is **not** a pre-stats primer; we don't re-teach calculus. It is also **not** a measure-theoretic probability course — we use σ-algebras lightly and pragmatically, not as a foundation layer.

**Core thesis — stats from first principles, approachably.** Every chapter opens with a *concrete, nameable problem* (a misdiagnosed COVID test, a biased die, a noisy measurement) whose resolution motivates every definition. **Every term is defined on the page where it first appears** — no appeals to "you may recall from a prior stats class," because there *is* no prior stats class in this reader's journey. Notation is introduced with a worked example, not a formal table. Formulas are derived, not cited. When something is genuinely hard (e.g. Borel σ-algebras, measure-theoretic subtleties), we flag it honestly and offer the intuition that gets the reader 95% of the way, not the formalism that gatekeeps.

**Approachability isn't dumbing-down — it's respect for the reader's time.** A reader who took single-variable calculus once and remembers roughly what a derivative means should be able to follow every chapter with patience. Multivariable calc shows up in Ch 4 and later, and we re-derive partial derivatives and Jacobians in the chapter that needs them, starting from scratch.

---

## 2. Anchor textbooks and why

| Anchor | Role | Why |
|---|---|---|
| **Blitzstein & Hwang — *Introduction to Probability* (2nd ed, 2019)** | Probability foundation (Ch 1–5) | First-principles, story-based, famously intuitive. Free PDF at <https://projects.iq.harvard.edu/stat110/home>. Ch. 1–8 align 1-to-1 with our Ch 1–4. |
| **Wasserman — *All of Statistics* (2004)** | Statistical inference (Ch 5–10) | Compact, rigorous, one-volume. Covers frequentist + Bayesian + nonparametric. Free PDF via Springer for students. |
| *(Optional companion)* **DeGroot & Schervish — *Probability and Statistics*** | Secondary reference | When a topic needs a slower derivation (e.g., sufficiency, exponential families). |

**External companion for visuals:** 3Blue1Brown's *Essence of Probability* (planned series) and StatQuest (Josh Starmer) videos.

---

## 3. Paradigm ordering: why probability → frequentist → Bayesian

Not parallel (confuses readers). Not pick-one (loses half the field). Instead **layered**:

1. **Ch 1–5:** pure probability (agnostic to paradigm).
2. **Ch 6–9:** frequentist inference (MLE, CIs, hypothesis tests, regression). This is what 90% of applied work looks like, and it's concrete: you write down a likelihood and maximize.
3. **Ch 10:** Bayesian inference built on top. By now the reader has met likelihoods twice over and the switch to "multiply by a prior, normalize, interpret as a distribution" lands cleanly.

This mirrors Wasserman's own ordering.

---

## 4. Ten-chapter outline

Each row shows anchor readings and a one-line pitch. Source mapping is explicit so chapter `sources.md` files can be stamped out quickly.

| # | Chapter | Bretscher-equivalent (Blitzstein/Wasserman) | One-line pitch |
|---|---|---|---|
| 1 | **Foundations: sample spaces, counting, probability axioms, conditional probability** | Blitzstein Ch 1–2 | "Probability is a measure on a sample space; everything else is bookkeeping." |
| 2 | **Discrete random variables and their distributions** | Blitzstein Ch 3–4 | "A random variable is a measurable function Ω → ℝ; its distribution is the pushforward." |
| 3 | **Expectation, variance, covariance, moments** | Blitzstein Ch 4–6 | "Linearity of expectation — the single most useful identity in probability." |
| 4 | **Continuous distributions, joint distributions, change of variables** | Blitzstein Ch 5, 7–8 · Wasserman Ch 2 | "PDFs, Jacobians, the bivariate and multivariate normal. Matrices are introduced here as bookkeeping; no LA background needed." |
| 5 | **Convergence and the limit theorems (LLN, CLT)** | Blitzstein Ch 10 · Wasserman Ch 5 | "Why sample means concentrate, and why they're approximately normal. The bridge to statistics." |
| 6 | **Point estimation: MLE, method of moments, bias–variance** | Wasserman Ch 6, 9 | "Given data, what's the best guess at the parameter? Two answers, both calculus." |
| 7 | **Confidence intervals and the bootstrap** | Wasserman Ch 7, 8 | "Quantifying uncertainty — analytically (normal-based CIs) and computationally (resampling)." |
| 8 | **Hypothesis testing and p-values** | Wasserman Ch 10 | "p-values, power, Wald/LRT/score tests, multiple comparisons — and when not to trust any of it." |
| 9 | **Linear regression** (LA-optional sidebar) | Wasserman Ch 13 · (LA Ch 7 optional) | "Least squares = maximum likelihood under Gaussian noise. Derived algebraically; an optional sidebar recovers the geometric picture for readers who've done LA." |
| 10 | **Bayesian inference: priors, posteriors, MCMC preview** | Wasserman Ch 11 · Blitzstein Ch 9 | "Prior × likelihood, normalized. Conjugate pairs analytically; everything else via PyMC / Stan." |

Each chapter has an **anchor problem in §0** and a **summary in §N** (last section), just like the LA chapters.

### Possible future volume 2 (not part of the initial 10)

If the first 10 land well, follow-ups could cover: stochastic processes & Markov chains, time-series analysis, generalized linear models, nonparametric density estimation, causal inference (Pearl / Rubin), experimental design. Keep out of scope for v1.

---

## 5. Tooling stack

### Programming language — R only (single-language pivot, 2026-04-21)

- **📊 R notebook (canonical, and the only student-facing language).** Base R + `tidyverse` + `ggplot2`. Rendered as `.ipynb` via IRkernel for NotebookLM / GitHub preview consistency.
- **No Python in student-facing material.** Python is used only for repo tooling (`scripts/extract_pages.py`, hosting the JupyterLab server for the R kernel). The `pyproject.toml` is slimmed to `pypdf` + `pyyaml` + `jupyterlab`.
- **(No SageMath.)** Sage's stats story is thin; dropping it avoids redundancy.

### Why R only

R is the statistical lingua franca — it's what Blitzstein and Wasserman both speak natively, what most published stats papers use, and what every academic course expects. Students need *one* language they can read papers in, trust the exact primitives of (`choose`, `pbinom`, `pnorm`, `lm`), and publish reproducible analyses in. Committing fully to R rather than splitting attention across Python is a pedagogy call: depth over breadth. Students who need Python-first ML workflows can revisit individual chapters in Python afterward.

### Shared repo infrastructure (reuse from LA curriculum)

- `scripts/nlm-init-chapter.sh` — **copy verbatim** from the LA repo; same nlm CLI workflow.
- `scripts/extract_pages.py` — **copy and adjust** for Blitzstein / Wasserman page offsets.
- `pyproject.toml` — start from LA's and add stats libs.
- `CLAUDE.md` — same project conventions as LA.

---

## 6. Per-chapter deliverables (the template)

Copied from the LA repo's proven template, with Sage → R:

```
chapters/NN-chapter-slug/
├── README.md                 learning objectives, prereqs, file guide
├── notes.md                  first-principles walk (~600 lines, ~14 sections)
├── worked-examples.md        10 fully solved problems
├── exercises.md              10 problems with answers
├── sources.md                Blitzstein + Wasserman page ranges, TODOs
├── extracts.yaml             page-range manifest for scripts/extract_pages.py
├── code/
│   └── NN_slug.ipynb         R notebook (IRkernel: tidyverse + ggplot2 + simulation)
└── extracts/                 (fair-use PDF extracts of anchor text chapters)
```

**Top-level:**

- `README.md` (landing page with 10-row chapter table, same styling as LA repo)
- `notebooks.md` (NotebookLM links + Python/R notebook links)
- `CONTRIBUTING.md` (dev setup)
- `LICENSE`, `pyproject.toml`, `CLAUDE.md`

### NotebookLM per chapter

Same as LA: one public NotebookLM per chapter with audio overview, quiz, mind map, slide deck, infographic, briefing doc, study guide. Publish via `scripts/nlm-init-chapter.sh chapters/NN... --all`.

---

## 7. Quality bar (non-negotiable — lessons from LA Ch 5–7)

1. **First principles for everything above high-school stats.** HS descriptive stats (mean, median, variance concept, fair-coin/fair-die probability) is assumed and can be used without re-derivation. Everything above that — sample spaces, σ-algebras, RVs, distributions, expectation operators, independence, Bayes' rule, estimators, likelihoods, CIs, tests — is defined on the page where it first appears, including notation (Σ, ∫, `P(A | B)`, `E[X]`, `Var(X)`). If a formula above HS-stats level appears, it is derived, not cited.
2. **Approachable prose, rigorous content.** Write as if explaining to a smart friend who's a working engineer, not as if writing a paper for a journal. Short sentences. Concrete examples before abstract statements. The LA curriculum's Ch 1–7 set the tone — match it.
3. **Anchor problem in §0 of every chapter.** A concrete, named scenario the whole chapter answers. Examples: "A hospital reports 60% of positive COVID tests are false positives — is that bad?" (Ch 1, Bayes). "Your sample mean is 3.2 with n=30 — what can you conclude about μ?" (Ch 6, MLE + CI). "Did this drug lower blood pressure more than placebo?" (Ch 8, hypothesis testing).
4. **Smoke-test arithmetic BEFORE publishing.** Every numerical answer in `worked-examples.md` and `exercises.md` must be verified by a `/tmp/smoke_chN.R` (base R's `choose`, `factorial`, `pbinom`, `pnorm`, plus `MASS::fractions()` for exact rationals) **before building the R notebook or NotebookLM**. This caught real arithmetic errors twice in the LA curriculum — see `~/Documents/source-code/learn-linear-algebra/memory/feedback_sage_as_referee.md`. Run via `Rscript /tmp/smoke_chN.R`.
5. **10 examples + 10 exercises, every chapter.** No skipping. Exercises include full answers (not "left as an exercise to the reader"). Difficulty ramps: first 3 are warm-up, middle 4 are standard application, last 3 push the reader.
6. **No ML/DL drift.** This is probability and statistics, not predictive modeling. Regression (Ch 9) is the closest we get. Bayes (Ch 10) is the Bayesian-inference kind, not "let's fit a neural net."
7. **Simulation as a pedagogical tool.** Every chapter from Ch 3 onward includes a Monte Carlo sanity check that visualizes the theorem numerically. Example: plot running means for LLN; plot histogram of standardized sample means converging to `φ(x)` for CLT. Simulation is cheap, visceral, and makes the math real — and it's a second way to verify the algebra is right.
8. **No gatekeeping prerequisites beyond calculus.** If a chapter needs a tool (partial derivatives, matrix multiplication, a particular integral identity), derive or motivate it in-chapter, even if briefly. Don't send readers to a textbook mid-chapter.

---

## 8. Suggested repo layout and first actions

```
~/Documents/source-code/learn-statistics/
├── README.md                            (landing page — 10 chapters table)
├── notebooks.md                          (NotebookLM + Python/R links)
├── CONTRIBUTING.md
├── LICENSE                               (same as LA curriculum)
├── CLAUDE.md
├── pyproject.toml                        (uv-managed; numpy, scipy, statsmodels, pymc, matplotlib, seaborn, jupyter)
├── scripts/
│   ├── nlm-init-chapter.sh               (copy from LA repo)
│   └── extract_pages.py                  (adjust page offsets for Blitzstein + Wasserman)
└── chapters/
    ├── 01-foundations/
    ├── 02-discrete-rvs/
    ├── 03-expectation-variance/
    ├── 04-continuous-and-joint/
    ├── 05-convergence-and-limit-theorems/
    ├── 06-point-estimation/
    ├── 07-confidence-intervals-and-bootstrap/
    ├── 08-hypothesis-testing/
    ├── 09-linear-regression/
    └── 10-bayesian-inference/
```

### Day-one startup sequence for the fresh Claude Code

1. `mkdir ~/Documents/source-code/learn-statistics && cd` there.
2. `git init`, create the scaffold above, stub every `chapters/NN-*/` directory with an empty `README.md`, `notes.md`, `worked-examples.md`, `exercises.md`, `sources.md`.
3. Copy `scripts/nlm-init-chapter.sh` from the LA repo.
4. Slim the Python `pyproject.toml` to tooling-only deps: `pypdf`, `pyyaml`, `jupyterlab` (to serve the R kernel). R-side deps (`IRkernel`, `tidyverse`, `ggplot2`, `gridExtra`) are installed per-user via `install.packages()` — not managed by uv.
5. Source the Blitzstein and Wasserman PDFs (both free — confirm URLs before downloading). Extract chapter ranges into `chapters/NN/extracts/` using `scripts/extract_pages.py`.
6. **Start Ch 1** using the following cadence (R-only):
   - Write `notes.md` → `worked-examples.md` → `exercises.md`.
   - Write `/tmp/smoke_ch1.R` (base R + `MASS::fractions()`) that verifies every numerical answer in the two documents.
   - Fix any prose errors exposed by the smoke test.
   - Build the R notebook at `chapters/01-foundations/code/01_foundations.ipynb`, execute with `uv run jupyter nbconvert --to notebook --execute --inplace`.
   - Publish NotebookLM with `--all`, delete any orphan notebooks from transient API retries.
   - Commit, push.

### Page-offset reference notes to set up once

| Book | PDF page offset to printed page | Notes |
|---|---|---|
| Blitzstein & Hwang 2nd ed | TBD — check first | Free PDF at <https://projects.iq.harvard.edu/stat110/home>. |
| Wasserman *All of Statistics* | TBD — check first | Springer; check university access. |

(LA repo's `memory/reference_pdf_offsets.md` documents this pattern for Bretscher +19 and Saveliev 0.)

---

## 9. What to reuse literally from the LA repo

- **`scripts/nlm-init-chapter.sh`** — the script is chapter-agnostic; no changes needed.
- **`scripts/extract_pages.py`** — change the book-to-offset mapping at the top.
- **`CLAUDE.md`** — same conventions: uv, 2-space indent, no pip, commit discipline, etc.
- **`CONTRIBUTING.md`** — same dev setup structure.
- **Chapter README template** — copy the structure (Learning objectives / Prerequisites / Estimated time / Files / Sources).
- **`notebooks.md` structure** — same 3-column table (NotebookLM / Python / R), plus the "What's in each" sections.
- **Landing README structure** — same 3-column table, same "how to run", same licensing/sources sections.

The LA curriculum's memory directory (`~/.claude/projects/-Users-prashantkulkarni-Documents-source-code-learn-linear-algebra/memory/`) documents several patterns that transfer:

- `feedback_first_principles.md` — every term defined in-chapter, every chapter opens with a concrete problem.
- `feedback_format_resonates.md` — the 7-file template is getting external compliments; keep the depth.
- `feedback_sage_as_referee.md` — **smoke-test every numerical answer before publishing**. For stats this means a SymPy + scipy `/tmp/smoke_chN.py`.
- `reference_nlm_cli.md` — `nlm` CLI is installed and authenticated under the user's account; the helper script wraps the per-chapter workflow.

Have the fresh Claude Code read all four before starting Ch 1.

---

## 10. Open decisions left for the fresh session to settle

- **R notebook format — SETTLED.** `.ipynb` with IRkernel (cleanly previews on GitHub, ingests into NotebookLM, reads the same as the R chunks in the prose). `.Rmd` not used.
- **Dataset companion.** LA had none. Stats wants recurring canonical datasets — suggest: `iris`, `mtcars`, `diamonds` (from ggplot2), `nhanes`, and a simulated dataset the curriculum generates once and reuses. Document in `datasets.md`.
- **Blitzstein vs. Wasserman on Markov chains and MCMC.** Blitzstein has a lovely Markov-chain chapter (Ch 11). Leaving it out of the initial 10 to keep scope tight; consider as Ch 11 of a v1.5 or volume 2.
- **Whether to do a Ch 0 "toolkit review."** Probably not — the floor is *"comfortable with calculus"*, and we teach anything beyond that in the chapter that needs it. The one place a light refresher might help is summation notation and basic set theory in Ch 1's notation section; fold that in-chapter rather than creating a Ch 0.
- **Bayesian computational backbone — SETTLED.** `rstanarm` and `brms` for Ch 10 (both are R packages that wrap Stan). This replaces the earlier `pymc` plan, consistent with the R-only pivot (2026-04-21). A "what this looks like in PyMC" appendix can be added later if useful for Python-first readers.

---

## 11. Estimated effort (based on LA cadence)

From LA metrics: one chapter takes **~1 session** of concentrated Claude Code work (notes + examples + exercises + two notebooks + NotebookLM + commit + push). Stats should be similar or *faster* per chapter with the single-language (R) pivot — one notebook instead of two.

10 chapters × ~1 session = **10 sessions**. The LA curriculum took roughly that cadence.

---

## TL;DR for the fresh Claude Code

> You are building a 10-chapter probability-and-statistics curriculum anchored on Blitzstein & Hwang and Wasserman, modeled on the sister repo at `~/Documents/source-code/learn-linear-algebra`. Per-chapter deliverables: `notes.md`, `worked-examples.md`, `exercises.md`, an R notebook (`code/NN_slug.ipynb`, IRkernel: tidyverse + ggplot2), `sources.md`, README — plus the NotebookLM publishing flow and R-based smoke-test-before-publish discipline (`Rscript /tmp/smoke_chN.R`).
>
> **Prerequisite floor: calculus + high-school stats.** Linear algebra is *not* assumed. Matrices are introduced just-in-time as bookkeeping, defined on the page where they first appear. Only Ch 9 (linear regression) has an *optional* LA sidebar for readers who've done the sister LA curriculum. Write for a smart reader who took calc once and remembers HS stats (mean, variance, histograms, basic coin/die probability) — approachable prose, rigorous content, every term above HS-stats level defined in-chapter.
>
> Start at `~/Documents/source-code/learn-statistics/`. Read the four feedback memories in the LA repo's memory directory before writing your first chapter. Ask the user for Blitzstein + Wasserman PDF access at the start of Ch 1 if they haven't already provided it.
