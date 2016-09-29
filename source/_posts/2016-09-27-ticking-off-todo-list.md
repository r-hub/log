---
title: This week's admin bits, ticking off the TODO list
date: 2016-09-27
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
