---
title: The first Windows Build
date: 2016-09-12
---

... just finished on the test server. It does have some glitches, but it is a start!

The main glitch is that the output is not streamed. This will require some more PowerShell tricks to fix. The problem is that the output is coming from another process, ran by another user, and this is apparently non-standard.

A bigger issue will be to work out how we should use `sysreqs` on Windows.
