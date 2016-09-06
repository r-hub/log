---
title: More Linux images
date: 2016-09-04
---

We have added three new Linux images with Jeroen:
* `linux-x86_64-centos6-epel`
* `linux-x86_64-centos6-epel-rdt`
* `linux-x86_64-rocker-gcc-san`

The last one runs the check with address sanitizer turned on.
Unfortunately the ASAN/UBSAN output is suppressed currently,
so we still need to work on this. :(

Some lessons learned, and improvements:
* The images must have `curl`, this is there on the CentOS6 images by
  default, but not on the Rocker images.
* The images must have `xvfb` as well.
* We are running xvfb now with a 1024x768 screen, and 24 bits color
  depth. This eliminates some R problems, that come up when not enough
  colors are available.
* The images may set the `RBINARY` environment variable. The ASAN image
  uses this to start the ASAN-augmented version of R, which is called
  `RD` on the image.
* Jenkins now starts `bash` in the Docker container with `-l`, to make
  it a login shell. This allows running `/etc/bashrc` in the image.
  the `epel-rdt` image runs the RDT config file this way.

### System requirements improvements

We have also added a bunch of new system requirements.
