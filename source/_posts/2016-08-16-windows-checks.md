---
title: Getting closer to Windows
---

### Powershell for the win

I am getting closer to supporting windows builds. I started to
write two Powershell scripts, that will eventually perform the
windows builds on the slaves: https://github.com/r-hub/wincheck

The first one `run.ps1`
* creates a new user with a random username,
* creates its home directory as well,
* makes sure that the home directory is not readable by other users,
* starts the build process (other script) as the newly created user,
  and makes sure that its standard output and error are redirected and
  still printed to the screen.

The other script is `slave.ps1`. This runs the actual `R CMD check`.
It all works now, but I need more features, e.g. installing dependencies.
In general it should do everything the unix version (at
https://github.com/r-hub/rhub-backend/blob/master/templates/jenkins.sh)
does.

Eventually these powershell scripts should go in the `rhub-backend`
repository I believe.

Somewhat surprisingly, Powershell is not very good at some sysadmin tasks,
e.g. creating local users is hard in plain Powershell. So for this we
require Carbon (http://get-carbon.org/) for now, this has to be installed
on the Windows builders.
