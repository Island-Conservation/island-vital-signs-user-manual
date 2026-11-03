User guide for Island Vital Signs (https://islandconservation-island-vital-signs.share.connect.posit.cloud/), a decision-support tool developed to turn satellite data into actionable insights for conservation projects on islands around the world.

This project is funded by Salesforce Nature Accelerator and NASA Biological Diversity and Ecological Conservation award 80NSSC23K1539 and includes team members from Island Conservation (Santa Cruz, California), Hobart and William Smith Colleges (Geneva, New York), South Dakota State University (Brookings, South Dakota), State University of New York College of Environmental Science and Forestry (Syracuse, New York), terraPulse (Gaithersburg, Maryland), and George Washington University (Washington D.C.).

The user guide is in prep and is located here: https://island-conservation.github.io/island-vital-signs-user-manual/

## Building the book

The book is written in [bookdown](https://bookdown.org/). In RStudio, open the project and use **Build → Build Book**, or run `bookdown::render_book("index.Rmd")` in the console. For a live-reloading preview while you write, run `bookdown::serve_book()`.

Rendering requires R with the `bookdown`, `rmarkdown`, `knitr`, `tidyverse` and `kableExtra` packages.

## Publishing

Pushing to `main` triggers the [publish workflow](.github/workflows/publish.yml), which renders the book and deploys it to GitHub Pages. The rendered HTML is **not** committed — do not edit anything in `docs/` by hand, and do not commit it.

Pull requests render the book as a check without deploying, so a broken build is caught before it reaches `main`.

This repo was initially generated from a bookdown template available here: https://github.com/jtr13/bookdown-template.
