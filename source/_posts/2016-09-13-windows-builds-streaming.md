---
title: Windows builds are streaming output
date: 2016-09-13
---

And it only took me half a day. :)

Lesson learned: in PowerShell, if you `WaitForExit` on a `Process`, then the no output events are generated until it finished. I guess it should be cannot `WaitForExitAndDoNothingElse`... anyway, this is fixed now [in this commit](https://github.com/r-hub/wincheck/commit/ac9d4fc3e7e7f3edda06d08a0a432659404bd1f9).

Another painful lesson is that apparently not everything is included in a generalized image on Azure. I did install Carbon, and more PowerShell stuff before capturing the image, but they were not present in the image. :( I guess I need to install them to some system directory, instead of the user's home.

Now I need to section the output on Windows, [this issue](https://github.com/r-hub/wincheck/issues/4)), and then make the output nicer
* in the `rhub` client,
* on the web,
* in the email message.
