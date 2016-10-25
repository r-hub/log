---
title: Small usability fixes
date: 2016-10-19
---

Typically small fixes, that improve user experience.

* Set `_R_CHECK_FORCE_SUGGESTS_` to `false` on Windows, by default.

* Allow overriding `R_CHECK_FORCE_SUGGESTS_` on Linux. (Previously it was set after the user supplied environment variables.)

* Use our CRAN mirror on Windows as well. For this I had to make it public, because the windows builders are classic Azure machines, so they are on a different internal network. I also installed HTTPS on it, via letsencrypt.

* Fixed the Jenkins job reaper, the rhub-cron process that removes old jobs from Jenkins.

* Updated the R-hub API, so that a job can be submitted for multiple platforms. This is needed for the `check_for_cran()` function in `rhub`.

* Added the `check_for_cran()` function to the `rhub` package. It runs the checks on 3-4 platforms, depending on whether the package has compiled code.
