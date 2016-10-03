---
title: This week's admin bits, ticking off the TODO list
date: 2016-10-03
---

## Build time limits

It is ten minutes without output currently. The time limit plugin does not allow me to set an additional hard limit as well...

## Passing arguments to `R CMD check`

Works now on Linux. Need to write some PowerShell to make it work on Windows: https://github.com/r-hub/wincheck/issues/6

## Docker cleanup on the image builder

The docker image builder cleans up old images and containers now.
The builders should do the same, ideally.

## Installing valgrind

On all ~15 Linux images, to make sure it works.

## `rhub` package fixes:

* Allow the email tokens from the web app in the CLI app as well: https://github.com/gaborcsardi/rhub/issues/8 The other way does not work currently.

* Checking tarballs works now: https://github.com/gaborcsardi/rhub/issues/14

* SAN runs work fine now: https://github.com/r-hub/rhub-backend/issues/10 https://github.com/gaborcsardi/rhub/issues/9

* I checked that the package works fine on Windows / OSX / Linux. We'll have proper CI at some point...

* Wrote a bunch of test cases. Found and fixed a couple of small bugs as well. https://github.com/gaborcsardi/rhub/issues/24

* Wrote higher level wrapper functions, so one can just say `check_on_windows()` instead of selecting the exact platform. https://github.com/gaborcsardi/rhub/issues/20 https://github.com/gaborcsardi/rhub/issues/22

* One can pass `R CMD check` arguments, e.g. `--valgrind` to the builder, and also environment variables. https://github.com/gaborcsardi/rhub/issues/4

## Windows

* There is a nice image now, called `win-builder-2016-10-03` that has everything installed, hopefully.

* The image has `r-oldrel`, `r-release`, `r-patched` and `r-devel` installed, and the frontend and backend also has everything needed to support them.

## HTTP/2

* It turns out that the stable nginx version (0.10.x) does not handle http/2 properly, so I had to turn it off. More details: https://github.com/gaborcsardi/rhub/issues/19 https://github.com/curl/curl/issues/1040 https://github.com/dokku/dokku/issues/2435

So currently I still use nginx 0.10.x with http/2 turned off.
