# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

A **bookdown** (R Markdown) book: the user manual for [Island Vital Signs](https://islandconservation-island-vital-signs.share.connect.posit.cloud/), an Island Conservation decision-support tool that turns satellite observations into actionable insights for island conservation. Funded by Salesforce Nature Accelerator and NASA award 80NSSC23K1539.

Published at https://island-conservation.github.io/island-vital-signs-user-manual/ via GitHub Pages. Chapters 1–3 are drafted; Chapter 4 (the use case studies, Sections 4.1–4.4) is a set of "Coming soon..." stubs.

This is **prose, not software**. Most work here is writing and formatting chapters, not programming.

## Build

R is installed but not on `PATH` in this shell, and `rmarkdown` cannot find pandoc on its own — the copy that ships with RStudio has to be pointed at explicitly. Both are needed:

```bash
export RSTUDIO_PANDOC="/c/Program Files/RStudio/resources/app/bin/quarto/bin/tools"
"/c/Program Files/R/R-4.5.3/bin/Rscript" -e 'bookdown::render_book("index.Rmd")'
```

From RStudio neither step is needed — Build → Build Book, or `bookdown::render_book("index.Rmd")` in the console, since RStudio puts both on the path.

R 4.5.3 with bookdown 0.46, knitr, tidyverse and kableExtra are all installed. Output lands in `docs/` (set by `output_dir` in `_bookdown.yml`), and a single-file `docs/islandvitalsigns.html` is also produced.

To preview, open `docs/index.html`, or run `bookdown::serve_book()` for a live-reloading local server.

## Publishing

[.github/workflows/publish.yml](.github/workflows/publish.yml) renders the book on every push to `main` and deploys it to GitHub Pages. Pull requests render it as a check without deploying.

`docs/` is **gitignored** — the rendered book is a build artifact, not source. Never commit it, and never hand-edit it; it exists locally only so you can preview. Publishing means merging to `main`, not rebuilding and committing output.

The Pages source in repo settings must stay on **GitHub Actions**. If it is ever switched back to "deploy from a branch", the site will 404 because `docs/` is no longer in the repo.

## Repository layout

| Path | Purpose |
| --- | --- |
| [index.Rmd](index.Rmd) | YAML front matter (title, authors), preface, acknowledgements, contacts |
| `01-introduction.Rmd` … `04-case-studies.Rmd` | Chapters, rendered in the order listed in `_bookdown.yml`. Each file is one chapter (`#`); all four case studies live as `##` sections inside `04-case-studies.Rmd` |
| [_bookdown.yml](_bookdown.yml) | Book filename, `output_dir: docs`, **explicit `rmd_files` list**, edit/view links |
| [_output.yml](_output.yml) | `bookdown::gitbook` output config and TOC customization |
| [style.css](style.css) | Small CSS overrides for captions and code wrapping |
| [images/](images/) | Figures referenced by `knitr::include_graphics()` |
| [tables/](tables/) | CSVs read into chapters, e.g. `island_lookup_table.csv` |
| `docs/` | **Generated and gitignored** — local preview only; CI builds what gets published |
| [.github/workflows/publish.yml](.github/workflows/publish.yml) | Renders the book and deploys it to GitHub Pages |
| `claude-resources/` | **Gitignored** source material (Word drafts, numbered figures, tables) to draft chapters from |

## Conventions that matter here

- **New chapters must be added to `rmd_files` in [_bookdown.yml](_bookdown.yml).** A file that is not listed is silently skipped.
- **Chapter filenames are numbered** (`01-`, `02-`, …) but the render order comes from `rmd_files`, not the filenames. Keep the two consistent.
- **Output HTML filenames are derived from the chapter's `#` heading**, so renaming a heading creates a new file in `docs/` and leaves the old one behind as an orphan. `git rm` the stale file whenever you rename a heading — a build will not clean it up.
- **After restructuring chapters, do a clean rebuild**: delete `docs/*.html`, `docs/reference-keys.txt` and `docs/search_index.json` first. `reference-keys.txt` persists across builds, so a section ID it already lists gets a `-1` suffix on the next build, producing empty pages like `monitoring-vegetation-growth-lehua-usa-1.html`.
- **Code chunks are hidden.** Figures use `echo=FALSE` with a `label=` and usually `out.width` / `fig.align` / `fig.cap`:

  ````markdown
  ```{r echo=FALSE, label="workflow-graphic", out.width='100%', fig.align='center'}
  knitr::include_graphics("images/workflow.jpg")
  ```
  ````

- **Tables** are read from `tables/` with `read.csv(..., check.names = FALSE)` and rendered with `kableExtra::kbl()` + `kable_styling()` — see `02-methods.Rmd`.
- **Use `kbl()`, never `knitr::kable()`, when piping into `kable_styling()`.** `kable_styling()` reformats the caption so bookdown no longer recognises the label `kable()` already inserted, and adds a second one — the caption renders as "Table 2.1: Table 2.2: …". `kbl()` does not have this problem.
- **Figure and table numbers are generated by bookdown** (`Figure 2.3`, `Table 2.1`), not written by hand, so `fig.cap`/`caption` text must not repeat the number. These do not match the section-level numbering used in the Word drafts (`Figure 2.3.1`); that is expected.
- **Citations are inline markdown links to the DOI or publisher page** (`[Gao 1996](https://…)`), not BibTeX. There is no `.bib` file.
- **Math is MathJax**: `$…$` inline, `$$…$$` display.
- Paths in chunks are **relative to the project root**, not to the `.Rmd` file.
- Content CSVs exported from Word arrive as **Windows-1252**; convert to UTF-8 before putting them in `tables/`.

## claude-resources/

Staging area holding the authoritative drafts (`User manual draft.docx`, `User manual sections 0-3.docx`) plus figures and tables named by the section they belong to (`figure2.6.1_…`, `section2.8_…`). Use it as the source when filling in stub chapters. It is **gitignored** — it lives on the author's machine only, so anything a chapter needs must be copied into `images/` or `tables/` to survive a fresh clone.

Copy assets into `images/` or `tables/` when they go into the book, keeping the `figureX.Y.Z_name` convention; do not reference `claude-resources/` paths from a chapter.

To read the Word drafts, convert them with the pandoc that ships with RStudio:

```bash
"/c/Program Files/RStudio/resources/app/bin/quarto/bin/tools/pandoc.exe" \
  -f docx -t gfm --wrap=none "claude-resources/User manual draft.docx" -o draft.md
```

## Working agreement

- Work on the `claude` branch; do not commit to `main`.
- Commit or push only when asked.
- This is published, attributed scientific writing with named authors. Do not invent findings, numbers, citations, or island data — draw from `claude-resources/` or ask.
- Island Conservation branding applies to any styled output (see the `ic-branding` skill).
