# Contributing

Thanks for considering a contribution. The fastest way to help: pick a chapter, read it, then open an issue or PR with anything that's unclear, missing, or could be sharper.

## Repo layout

```
learn-statistics/
  README.md                       # consumer-facing entry point
  CONTRIBUTING.md                 # this file
  notebooks.md                    # public NotebookLM index
  LICENSE                         # MIT
  pyproject.toml                  # uv project — tooling only (pypdf + pyyaml + jupyterlab)
  uv.lock                         # locked dependency versions
  books/                          # gitignored — bring your own PDFs
  chapters/NN-topic/
    README.md                     # chapter objectives
    notes.md                      # main tutorial
    worked-examples.md            # 10 solved problems
    exercises.md                  # 10 problems + answers
    sources.md                    # page references and external links
    extracts.yaml                 # which pages to slice from source PDFs
    extracts/                     # gitignored — generated PDFs
    code/
      NN_slug.ipynb               # R notebook (IRkernel: tidyverse + ggplot2)
    visuals/                      # diagram references
  scripts/
    extract_pages.py              # PDF excerpter (uv run)
    nlm-init-chapter.sh           # publish a chapter to NotebookLM
```

## Why R only?

R is the statistical lingua franca: it's what Blitzstein and Wasserman both speak natively, what most published stats papers use, and what every academic course expects. This repo commits fully — every worked example, every plot, every simulation is in R. Python appears only in the build tooling (`scripts/`), never in student-facing material.

## What you need to develop locally

You only need this setup if you want to run the R notebooks, regenerate a NotebookLM, or modify content with the publishing pipeline. **For just reading or fixing typos in the markdown, no setup is required.**

### 1. Get the source PDFs

The two anchor textbooks are copyrighted and **not** redistributed in this repo. Obtain your own copies and place them at:

```
books/Blitzstein.pdf
books/Wasserman.pdf
```

Both filenames are in `.gitignore`. The PDF extractor also accepts the original z-lib / Springer filenames if you haven't renamed — see `scripts/extract_pages.py`'s `BOOKS` dict.

- Joseph K. Blitzstein and Jessica Hwang, *Introduction to Probability*, 2nd ed. (CRC Press, 2019). A free companion PDF is linked from the authors' Stat 110 site: <https://projects.iq.harvard.edu/stat110/home>.
- Larry Wasserman, *All of Statistics* (Springer, 2004). Check your university's Springer access.

### 2. R + IRkernel (for the student-facing notebooks)

```bash
# macOS: install R if you don't have it yet.
brew install --cask r
```

Then, from an R console, install the kernel once:

```R
install.packages(c("IRkernel", "tidyverse", "ggplot2", "gridExtra"))
IRkernel::installspec()
```

`IRkernel::installspec()` registers the R kernel with Jupyter so `uv run jupyter lab` will offer an "R" option in the kernel dropdown.

### 3. Python tooling (uv)

This repo's Python setup is minimal — only what's needed to serve Jupyter and extract PDF pages.

```bash
uv sync                        # installs pypdf + pyyaml + jupyterlab
uv run jupyter lab             # opens the browser; pick "R" for any notebook
```

Add new *tooling* deps with `uv add <package>`. Do **not** add Python libraries for stats content (numpy, scipy, pymc, etc.) — all stats content is R.

### 4. NotebookLM CLI (only if regenerating notebooks)

The publishing pipeline uses the [`nlm` CLI](https://github.com/jacob-bd/notebooklm-mcp-cli). Install and authenticate it once:

```bash
pipx install notebooklm-mcp-cli
nlm login          # opens a browser; sign into your Google account
nlm doctor         # confirms auth and prerequisites
```

## Smoke-test discipline (non-negotiable)

Before publishing a chapter, write and run `/tmp/smoke_chN.R` that checks *every numerical answer* in `worked-examples.md` and `exercises.md` using base R's exact primitives (`choose`, `factorial`, `pbinom`, `pnorm`, etc.) and — for rationals — `MASS::fractions()`. This is the R analogue of the sister LA curriculum's SageMath smoke test; it has caught real arithmetic errors twice in that repo. Fix the prose before publishing.

Example pattern:

```R
# /tmp/smoke_ch1.R — smoke-test every numerical answer in Ch 1 prose.
library(MASS)

check <- function(label, got, expected, tol = 1e-9) {
  ok <- abs(got - expected) < tol
  if (!ok) stop(sprintf("%s: got %.12g, expected %.12g", label, got, expected))
  cat(sprintf("  ✓ %s = %.6g\n", label, got))
}

# Example 1: birthday-problem probability for k=23
p_bday_23 <- 1 - prod((365 - 0:22) / 365)
check("E1 birthday(23)", p_bday_23, 0.5072972, tol = 1e-6)

# Example 2 (exact rational):
cat("E2 prob = "); print(fractions(choose(5, 2) / choose(7, 4)))

cat("Ch 1 smoke test passed.\n")
```

Run with `Rscript /tmp/smoke_ch1.R`.

## How a chapter gets published to NotebookLM

Two steps once you have the PDFs in place.

**Step 1 — Extract the page ranges from the source PDFs.** Each chapter has an `extracts.yaml` listing which Blitzstein/Wasserman page ranges to slice out:

```bash
uv run scripts/extract_pages.py chapters/01-foundations
# writes chapters/01-foundations/extracts/*.pdf
```

The extractor handles per-book offsets (Blitzstein +17, Wasserman +16). Outputs are gitignored.

**Step 2 — Create the NotebookLM notebook + artifacts + share publicly.**

```bash
# Markdown sources only
./scripts/nlm-init-chapter.sh chapters/01-foundations

# Markdown + extracted PDF excerpts (no artifacts)
./scripts/nlm-init-chapter.sh chapters/01-foundations --with-extracts

# Full pipeline: extracts + audio + quiz + mind map + slides + infographic + reports + public share
./scripts/nlm-init-chapter.sh chapters/01-foundations --all

# Show what would happen, without doing it
./scripts/nlm-init-chapter.sh chapters/01-foundations --all --dry-run
```

Individual artifact flags (`--audio`, `--quiz`, `--mindmap`, `--slides`, `--infographic`, `--reports`, `--share`) can be combined.

After `--all` finishes, copy the printed notebook URL into:

- the *Chapter map* table in `README.md`
- the corresponding row in `notebooks.md`
- the chapter's own `chapters/NN/README.md`

## Style guide for the markdown

- Tone: self-study, intuition first, rigor without gatekeeping. Write for a reader who took calculus once and remembers high-school stats (mean, variance, histograms, basic coin/die probability).
- Every chapter has: `notes.md`, `worked-examples.md` (10 examples), `exercises.md` (10 problems with answers), `sources.md`, and an R notebook in `code/`.
- Every concept has at least one worked example and one exercise.
- Define every term above HS-stats level the first time it appears in-chapter.
- Cite Blitzstein and Wasserman page ranges generously.
- Use ASCII or LaTeX-style math (`P(A ∩ B)`, `E[X]`, `Var(X)`, `μ`, `σ²`).
- Don't redistribute textbook prose or figures. Reference them by page number.

## Submitting changes

1. Fork the repo, make a branch.
2. Make the change. If you touched an R notebook, re-execute it (`uv run jupyter nbconvert --to notebook --execute --inplace <file>.ipynb`).
3. If the change touches a published chapter's `notes.md`, `worked-examples.md`, or `exercises.md`, re-publish that chapter's NotebookLM with `--all` (or just re-upload the changed source) so the public notebook stays in sync.
4. Open a PR. Small, focused PRs are preferred.

## Code of conduct

Be kind. Assume good faith. Help newcomers.
