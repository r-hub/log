---
title: Binary cache & Windows
---

### Some more Linux builders

* `r-patched-linux-x86_64` Debian testing
* `r-devel-linux-x86_64-ubuntu-16.04` most recent Ubuntu LTS
* `r-release-linux-x86_64-ubuntu-16.04` most recent Ubuntu LTS
* `r-devel-linux-x86_64-debian-jessie` stable Debian
* `r-release-linux-x86_64-debian-jessie` stable Debian

Adding new images is somewhat complicated. I need to add them at several
places:
* Add the Dockerfile to the rhub-linux-builders repo.
* Create a repository on Docker Hub.
* Add an entry to `jenkins.sh` in the same repo, to build it periodically.
* Add an entry to `public/data/platforms.json` on the rhub-frontend repo.
* Add it to the Jenkins job that pulls images to the workers.
* Run the Jenkins job that builds the images, and/or build them by hand
  and push them to Docker Hub
* Add the platform to the sysreqs database, if it is not there yet.
  Update the database from GitHub.

Then one needs to re-reploy the frontend (!).

We need some central configuration store, like `etcd`, or something
similar, to avoid this lengthy and error-prone procedure.

### Windows builders

Jenkins has an Azure plugin. Unfortunately it uses the 'classic' system,
which is soft-deprecated on Azure, and the branch to update it to the
new Azure Resource Manager seems to be dead.

After a couple of hours I could get it work. Key points:
- Use the JNLP launch method. Much easier than SSH.
- The JNLP port needs to be fixed to 50000, but it was already
  fixed, anyway.
- Leave the 8080 and 50000 ports open on the master. These must be
  restricted to the slave IP addresses in the future.
- A new user 'slave' is needed in Jenkins, and its name and token
  must be added to the example init file.
- The new user needs capabilities: overall/read, slave/connect and
  slave/disconnect.

As for provisioning, I'll write a short powershell script that downloads
the real provisioning script from GitHub, similarly to how r-appveyor
works.
