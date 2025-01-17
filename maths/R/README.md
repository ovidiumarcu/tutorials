Introduction to R
================

[![By
ULHPC](https://img.shields.io/badge/by-ULHPC-blue.svg)](https://hpc.uni.lu)
[![Licence](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](http://www.gnu.org/licenses/gpl-3.0.html)
[![GitHub
issues](https://img.shields.io/github/issues/ULHPC/tutorials.svg)](https://github.com/ULHPC/tutorials/issues/)
[![](https://img.shields.io/badge/slides-PDF-red.svg)](https://github.com/ULHPC/tutorials/raw/devel/maths/R/PS11_targets.pdf)
[![Github](https://img.shields.io/badge/sources-github-green.svg)](https://github.com/ULHPC/tutorials/tree/devel/maths/R/)
[![Documentation
Status](http://readthedocs.org/projects/ulhpc-tutorials/badge/?version=latest)](http://ulhpc-tutorials.readthedocs.io/en/latest/maths/R/)
[![GitHub
forks](https://img.shields.io/github/stars/ULHPC/tutorials.svg?style=social&label=Star)](https://github.com/ULHPC/tutorials)

------------------------------------------------------------------------

# R Tutorial

      Copyright (c) 2013-2021 Aurelien Ginolhac, UL HPC Team  <hpc-sysadmins@uni.lu>

[![](https://github.com/ULHPC/tutorials/raw/devel/maths/R/img/cover_slides.png)](https://ulhpc-tutorials.readthedocs.io/en/latest/maths/R/PS11_targets.html)

<div class="shareagain" style="min-width:300px;margin:1em auto;">

<iframe src="PS11_targets.html" width="750" height="421" style="border:2px solid currentColor;" loading="lazy" allowfullscreen></iframe>
<script>fitvids('.shareagain', {players: 'iframe'});</script>

</div>

Through this tutorial you will learn how to use R from your local
machine or from one of the [UL HPC platform](https://hpc.uni.lu)
clusters. Then, we will see how to organize and group data. Finally we
will illustrate how R can benefit from multicore and cluster
parallelization.

Warning: this tutorial does not focus on the learning of R language but
aims at showing you nice start-up tips. If you’re also looking for a
good tutorial on R’s data structures you can take a look at: [Hadley
Wickham’s page](http://adv-r.had.co.nz/Data-structures.html). Another
[bookdown](https://bookdown.org/)’s book is available for free: [R for
Data Science](http://r4ds.had.co.nz/index.html) by Garrett Grolemund &
Hadley Wickham

------------------------------------------------------------------------

## Pre-requisites

Ensure you are able to [connect to the UL HPC
clusters](https://hpc-docs.uni.lu/connect/ssh/)

**you MUST work on a computing node**

``` bash
# /!\ FOR ALL YOUR COMPILING BUSINESS, ENSURE YOU WORK ON A COMPUTING NODE
(access-iris)$> si -c 2 -t 1:00:00
```

### Optional: On your local machine

First of all, let’s install R. You will find releases for various
distributions available at [CRAN Archive](http://cran.r-project.org/).

You will also find handy to use the [R-Studio](https://www.rstudio.com/)
graphical IDE.

### On HPC, available as a module

``` bash
module load lang/R
```

We also need `pandoc`, supplied in RStudio but not on HPC.

We fetch the binary and copied in our own `~/bin` that should be
included in your `PATH`.

``` bash
wget -qO- https://github.com/jgm/pandoc/releases/download/2.16.1/pandoc-2.16.1-linux-amd64.tar.gz | tar xfz - 
mkdir -p ~/bin/
cp pandoc-2.16.1/bin/pandoc ~/bin/
```

### Cloning

1.  If you haven’t cloned the
    [`tutorials`](https://github.com/ULHPC/tutorials) repository, do it
    with `git clone https://github.com/ULHPC/tutorials.git`

2.  In your cloned [`tutorials`](https://github.com/ULHPC/tutorials)
    repository, `cd tutorials/maths/R`

3.  Open a R session `jdoe@localhost:~$ R` or create a New Project in
    RStudio

### Enclosed R packages environment

We will use [`renv`](https://rstudio.github.io/renv/articles/renv.html)
to synchronize the practical packages with yours. Having a dedicated
library project allows to retrieve a certain specific set without
messing other projects

Of note, the `.Rprofile` is detected when `R` is started in the
`tutorials/maths/R` folder and should see automatic boostraping of
`renv`:

``` r
# Bootstrapping renv 0.14.0 --------------------------------------------------
* Downloading renv 0.14.0 ... OK (downloaded source)
* Installing renv 0.14.0 ... Done!
* Successfully installed and loaded renv 0.14.0.
* Project '/mnt/lscratch/users/aginolhac/tutorials/maths/R' loaded. [renv 0.14.0]
* The project library is out of sync with the lockfile.
* Use `renv::restore()` to install packages recorded in the lockfile.
Warning message:
Project requested R version '4.1.0' but '4.0.5' is currently being used
```

Otherwise:

4.  Install `renv`

``` r
install.packages("renv")
```

Then, restore the package list locally:

5.  Restore the packages set for this practical

``` r
renv::restore()
```

The list is prompted:

``` r
[...]
# GitHub =============================
- tarchetypes      [* -> ropensci/tarchetypes@main]
- targets          [* -> ropensci/targets@main]

Do you want to proceed? [y/N]: 
```

Enter `y` and wait (takes 5 to 10 minutes)

The packages will be copied in a local cache, shared across projects. So
the same package installed in another folder is only linked, saving a
lot of time while preserving the enclosed packages environments.

It takes time but who is really using R with `base` only?

6.  Run `targets` for the **datasauRus** example

Either knitr in RStudio the file `datasauRus.Rmd` or run
`rmarkdown::render("datasauRus.Rmd")` in a console.

7.  Run `targets` for the **gapminder** example

Either knitr in RStudio the file `gapminder.Rmd` or run
`rmarkdown::render("gapminder.Rmd")` in a console.

-   `gapminder.html` document both the pipeline description and run
-   `report_gap.html` was dynamically rendered

7bis. Try to set the multi-process on using `future`

Un-comment the lines

``` r
library(future)
#library(future.callr)
#plan(callr)
```

and replace `tar_make()` by

``` r
tar_make_future(workers = 2)
```

8.  Re-Knit `gapminder.Rmd`, all targets are skipped.

9.  Modify one value in `gapminder.tsv` the raw data and Re-knit
    `gapminder.Rmd`

10. Change the linear regression for `lifeExp ~ year1950 + gdpPercap`
    and Re-knit `gapminder.Rmd`

## Useful links

-   [CRAN Archive](https://cran.r-project.org/)
-   [CRAN HPC
    Packages](https://cran.r-project.org/web/views/HighPerformanceComputing.html)
-   [Tidyverse Documentation](https://tidyverse.org/)
-   [4-days tidyverse workshop.uni.lu](https://rworkshop.uni.lu/)
-   [Advanced R programming by Hadley Wickham](http://adv-r.had.co.nz/)
