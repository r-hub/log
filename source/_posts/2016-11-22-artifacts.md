---
title: Build artifacts
date: 2016-11-22
---

If you looked at the R-hub notifications in the past two weeks, you might have noticed that at the bottom of the email there is a new link called "artifacts". This link points to a temporary web space, that contains the build artifacts of your R package:
- The `00check.log` file of the check output.
- The `00install.out` file, the output of the installation of the package. This is often helpful if the installation fails, as the normal check output omits the details in this case.
- Output of the tests.
- Source R package. This should be the same as the uploaded one.
- Binary R package for your platforms.
- Binary packages for all dependent packages that were built from source. This includes packages specified via the [`Remotes` mechanism](https://cran.rstudio.com/web/packages/devtools/vignettes/dependencies.html).
