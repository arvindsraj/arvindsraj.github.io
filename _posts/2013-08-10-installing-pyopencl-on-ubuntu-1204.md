---
layout: post
title: "Installing pyopencl on Ubuntu 12.04"
description: "A simple how-to for installing pyopencl on Ubuntu 12.04"
author: "Arvind"
category: pyopencl
tags: [python, pyopencl, ubuntu]
---

This post is a simple guide on how to install pyopencl on Ubuntu 12.04.

<!--more-->

### Before you install according to the following steps
**Note: If you running Ubuntu 12.04(with an nVidia card), Ubuntu 12.10 or
  later, python-pyopencl from the repos should work fine.**

The reason why I decided to write this post is because I couldn't find very
many good guides that offer installation instructions for machines which do not
have an nVidia card and running Ubuntu 12.04. Also, the package python-pyopencl
from the 12.04 repos doesn't work since it depends on nvidia
packages(nvidia-current and nvidia-settings), which broke desktop effects since
I don't have an nVidia GPU. It's always good to check if this has been fixed by
backporting changes from newer versions of Ubuntu before continuing. See this
[bug report](http://bugs.launchpad.net/ubuntu/+source/fglrx-installer/+bug/763457/)
for possible updates on the backporting status.

### Installing OpenCL
I installed the Intel CPU ICD. If you wish to use the AMD CPU/GPU ICD, please
follow instructions in \[1\]. Intel OpenCL implementation supports only 64bit
machines so you'll need the AMD ICD(it works fine on Intel CPUs too) if you run
a 32 bit machine.

* Download and untar the **runtime package** from [Intel's
  website](http://software.intel.com/en-us/vcsource/tools/opencl-sdk-xe).
* Install the alien package from the Ubuntu repos. 
* Convert the rpm packages using alien and fakeroot.
  {% highlight bash %}
    fakeroot alien --to-deb --scripts *.rpm
  {% endhighlight %}
* Install the newly create deb packages using dpkg.
* Install the package opencl-headers(if you installed the SDK, this is
  automatically installed) from the Ubuntu repos.
* See if this [sample program provided by
  Intel](http://software.intel.com/en-us/vcsource/samples/caps-basic/) compiles
  and executes successfully.  
  _Note\: If you get an error saying shared library not found when executing
  the binary, creating symlinks to /usr/lib64/libOpenCL.so\[.1, .2\] in
  /usr/lib/ should fix it since Ubuntu expects the shared objects to be in
  /usr/lib/._

### Installing pyopencl
I installed pyopencl and any additional packages required from PyPI. pip is
provided the package python-pip.

* Install the package python-setuptools from the Ubuntu repos(required to build
  pyopencl when installing using pip).
* Now, install numpy and Mako from PyPI(using pip) or from Ubuntu repos.
* Next install pyopencl using pip.
  {% highlight bash %}
    sudo pip install pyopencl
  {% endhighlight %}
* Open the python prompt and try importing pyopencl. If it succeeds, you have
  successfully installed pyopencl.

### References

\[1\] [Installation instructions from OpenCL maintainer](http://wiki.tiker.net/OpenCLHowTo)

\[2\] [Michael Hruby's guide to installing OpenCL on Ubuntu 13.04](http://mhr3.blogspot.com/2013/06/opencl-on-ubuntu-1304.html)
{% include JB/setup %}
