---
title: News, big and small
date: 2016-10-18
---

## Biggish changes

### Nice emails.

The email sending happens on the front-end app now, Jenkins only sends a signal that the build is done. This gives us much more flexibility in formatting the emails.

![](/images/nice-email-1.png)
![](/images/nice-email-2.png)

### Log database

The check logs are stored in a log database. This allows a better API, e.g. querying checks for an email address.

### New API endpoints

* Better `status` API, using the log database, it handles multiple checks at once.
* `livelog` to supply the build log in a non-stream format.

### `rhub` package improvements

* `status` API, to query the status of a build. It also handles multiple checks.
* `list` API, to list checks for an email address and/or a package.
* Cache id of last submission. So the status can be checked easily.
* Platform chooser. This is used by default if no platform is specified and the R session is interactive.
* Revamped the email validation API.

## Smaller changes and Bug fixes

* Fixed the `f90` compiler on the Debian builders. We need to set the `FC` option when compiling R.

* Created a repository with small test packages, and known check output: https://github.com/r-hub/test-packages

* We use our internal CRAN mirror, to avoid external traffic, and speed up the check. Both on Linux and Windows.

* Avoid streaming the Jenkins log, this gives a nicer API for the R client. For example it avoids splitting into lines in R. This happends in JS now, and it is more reliable. It also reduces the traffic.

* Handle `ABORTED` builds. These are typically timeouts because of no output.

* Set _R_CRAN_FORCE_SUGGESTS_ to false on all builders, but allow overriding it.

* Fixed the Jenkins job reaper that runs every hour and removes jobs older than three days.
