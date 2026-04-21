# Public NotebookLM Notebooks + R Notebooks

One pre-built NotebookLM per chapter. Open the link to read the notes, listen to the audio overview, take the quiz, browse the mind map, view the slide deck and infographic, or chat with the sources. Click *"Make a copy"* in NotebookLM to clone it into your own account and customize.

Each chapter also ships with a runnable **R notebook** (`.ipynb` using IRkernel, `tidyverse` + `ggplot2` + Monte Carlo simulation). Install the R kernel once, then open any chapter's notebook with `uv run jupyter lab`. See [CONTRIBUTING.md](CONTRIBUTING.md) for setup.

| # | Chapter | NotebookLM 🎧 | 📊 R notebook |
|---|---|---|---|
| 1 | Foundations: sample spaces, counting, probability axioms, conditional probability | [Open in NotebookLM ↗](https://notebooklm.google.com/notebook/bb199913-6520-4a21-95be-65c5dcd3dd5e) | [view ipynb](chapters/01-foundations/code/01_foundations.ipynb) |
| 2 | Discrete random variables and their distributions | _coming soon_ | _coming soon_ |
| 3 | Expectation, variance, covariance, moments | _coming soon_ | _coming soon_ |
| 4 | Continuous distributions, joint distributions, change of variables | _coming soon_ | _coming soon_ |
| 5 | Convergence and the limit theorems (LLN, CLT) | _coming soon_ | _coming soon_ |
| 6 | Point estimation: MLE, method of moments, bias–variance | _coming soon_ | _coming soon_ |
| 7 | Confidence intervals and the bootstrap | _coming soon_ | _coming soon_ |
| 8 | Hypothesis testing and p-values | _coming soon_ | _coming soon_ |
| 9 | Linear regression (LA-optional sidebar) | _coming soon_ | _coming soon_ |
| 10 | Bayesian inference: priors, posteriors, MCMC preview | _coming soon_ | _coming soon_ |

## What's in each NotebookLM

- The chapter's tutorial notes, worked examples, and exercises (markdown)
- Page extracts from the two anchor textbooks (Blitzstein and Wasserman)
- A generated **audio overview** (deep-dive podcast — listen on your commute)
- A generated **quiz** to self-test
- A generated **mind map** of the chapter's concepts
- A generated **slide deck** for review
- A generated **infographic** for at-a-glance reference
- A generated **Briefing Doc** and **Study Guide** (long-form summaries)
- Chat-with-sources interface for "explain X", "give me another example of Y", etc.

## What's in each R notebook

- **Simulation block** — Monte Carlo verification of the chapter's central claim. From Ch 3 onward this is the one place the math feels *alive*: run 10,000 experiments, plot the histogram, compare to the theoretical curve.
- **Analytic block** — exact computation via base R (`choose`, `factorial`, `pbinom`, `pnorm`, etc.) to cross-check every worked example's arithmetic.
- **Visualization block** — `ggplot2` plots of distributions, tree diagrams, contingency tables, convergence curves, QQ plots, and so on.
- **Exercises block** — a short set of R-first problems at the end with solutions, in addition to the pen-and-paper `exercises.md`.

R is the statistical lingua franca: it's what Blitzstein and Wasserman both speak natively, what most published stats papers use, and what every academic course expects.
