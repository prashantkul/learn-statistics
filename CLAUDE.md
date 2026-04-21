# Claude Code conventions for `learn-statistics`

This repo is a self-study probability-and-statistics curriculum modeled on the sister repo at `~/Documents/source-code/learn-linear-algebra`. Read `learn-statistics-plan.md` end-to-end before making substantive changes.

## Quality bar

1. **First principles for everything above high-school stats.** HS descriptive stats (mean, median, variance concept, reading a histogram, fair-coin/fair-die probability) is assumed. Everything above that — sample spaces, σ-algebras, RVs, distributions, expectation operators, independence, Bayes' rule, estimators, likelihoods, CIs, tests — is defined on the page where it first appears. Every formula is derived, not cited.
2. **Every chapter opens with a concrete anchor problem** (§N.0), and closes with a summary (§N.last). Anchor problems are nameable scenarios — "A hospital reports 60% of positive COVID tests are false positives — is that bad?" — whose resolution motivates every definition in the chapter.
3. **Smoke-test arithmetic before publishing.** Every numerical answer in `worked-examples.md` and `exercises.md` must be verified by `/tmp/smoke_chN.R` (base R `choose`, `factorial`, `pbinom`, `pnorm`, etc., plus `MASS::fractions()` for exact rationals where helpful) **before** building the R notebook or publishing NotebookLM. This discipline has caught real arithmetic errors twice in the sister LA curriculum.
4. **10 examples + 10 exercises per chapter, full answers included.** Difficulty ramps: first 3 warm-up, middle 4 standard application, last 3 push the reader.
5. **Prerequisite floor: calculus + HS stats.** No linear algebra assumed — matrices are introduced just-in-time as bookkeeping, defined on the page where they first appear. Only Ch 9 has an *optional* LA sidebar.
6. **Simulation as a pedagogical tool.** From Ch 3 onward, every R notebook includes a Monte Carlo sanity check that visualizes the theorem numerically (running means for LLN, histogram → `dnorm(x)` for CLT, etc.).
7. **No ML/DL drift.** This is probability and statistics. Regression (Ch 9) is the closest we get. Bayes (Ch 10) is Bayesian inference, not neural nets.

## Tooling

- **R is the only student-facing language.** All student code lives in `chapters/NN-slug/code/NN_slug.ipynb`, using the R kernel (IRkernel) inside Jupyter. Canonical stack: base R + `tidyverse` + `ggplot2`. No Python in student-facing material.
- **Python is repo infrastructure only.** `uv`-managed (`pyproject.toml` + `uv.lock`) with a minimal dep set — `pypdf` + `pyyaml` for `scripts/extract_pages.py`, plus `jupyterlab` to serve the R kernel. Never ship Python notebooks for stats content.
- **Run notebooks:** `uv run jupyter lab` from repo root; pick the "R" kernel when opening a chapter notebook.
- **Smoke tests are R:** `/tmp/smoke_chN.R`, invoked via `Rscript /tmp/smoke_chN.R`.
- **No SageMath.** Dropped intentionally vs. the sister LA repo.

## Chapter template (per `chapters/NN-slug/`)

```
README.md                 # learning objectives, prereqs, file guide
notes.md                  # first-principles walk, ~600 lines, ~14 sections
worked-examples.md        # 10 fully solved problems
exercises.md              # 10 problems with answers
sources.md                # Blitzstein + Wasserman page ranges, external links
extracts.yaml             # page-range manifest for scripts/extract_pages.py
code/
  NN_slug.ipynb           # R notebook: tidyverse + ggplot2 + simulation
extracts/                 # .gitignored — generated PDF excerpts
visuals/                  # diagram references
```

## Style

- 2-space indentation, ASCII or LaTeX-style math (`x²`, `ℝⁿ`, `P(A | B)`, `E[X]`).
- Short sentences, concrete examples before abstract statements.
- Cross-reference Blitzstein and Wasserman page numbers generously — readers are expected to read alongside.
- Never redistribute textbook prose or figures. Reference by page number.
- Do not commit `books/*.pdf` (they're in `.gitignore`) — the textbooks are copyrighted.

## Commit discipline

- Small, focused commits; one chapter section or one pipeline change per commit.
- Conventional-style prefixes: `feat:`, `fix:`, `chore:`, `docs:`.
- Never commit secrets. Never commit the source PDFs.

## Publishing a chapter (end-to-end)

```bash
# 1. Smoke-test the prose
Rscript /tmp/smoke_chN.R     # verifies every numerical answer

# 2. Execute the R notebook
uv run jupyter nbconvert --to notebook --execute --inplace \
  chapters/01-foundations/code/01_foundations.ipynb

# 3. Extract the page ranges from the source PDFs
uv run scripts/extract_pages.py chapters/01-foundations

# 4. Create NotebookLM + upload sources + generate all artifacts + share
./scripts/nlm-init-chapter.sh chapters/01-foundations --all
```

After publishing, copy the notebook URL into:
- `README.md` chapter-map table
- `notebooks.md` table
- the chapter's `README.md`
