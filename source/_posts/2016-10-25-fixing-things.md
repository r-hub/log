---
title: Fixing various bugs
date: 2016-10-25
---

## `rhub` package rewrite

I reorganized the `rhub` package, and created R6 classes to hold information about the checks. Much cleaner now. Functions `check()`, `check_for_cran()` `list_my_checks()`, `list_package_check()`,e etc. all return `rhub_check` objects.

There is also a new function `last_check()` that returns the last submitted check of the current R session.

## Bug fixes

* The web submission checks the submitted filename. If it does not look like a proper R source package, built via `R CMD build`, then we reject it immediately.

* On Linux platforms, we are now installing `pandoc` from the binaries built by RStudio. Unfortunately this still fails sometimes: https://github.com/r-hub/rhub-linux-builders/issues/10 https://github.com/r-hub/rhub-linux-builders/issues/15 We will have to build our own pandoc binaries or packages...

* `qpdf` is needed for checking PDF documentation. It is now installed on the Linux builders. Windows coming soon.

* The Jenkins job reaper does not fail on unusual jobs, e.g. ones that were aborted or that never ran for some reason.
