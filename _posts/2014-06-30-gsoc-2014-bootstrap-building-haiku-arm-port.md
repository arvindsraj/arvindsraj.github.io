---
layout: post
title: "[GSoC 2014] Bootstrap building Haiku ARM port"
description: "Steps to build bootstrap image of Haiku for beaglebone black"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Here is a quick guide to building a bootstrap MMC image of Haiku for a beaglebone black. These might get outdated in
future but for now, these are probably the latest steps.

I tried these on a fresh install of Kubuntu 14.04 64bit so it should work fine on any distro that uses apt. If
something is wrong/missing, do let me know and I'll make required changes.

<!--more-->

### Installing software required for building Haiku for ARM

The following are required for building Haiku.

{% highlight bash %}
$ sudo apt-get install git nasm autoconf automake texinfo flex bison gawk \
    build-essential unzip wget zip less zlib1g-dev libcurl4-openssl-dev
{% endhighlight %}

In addition, the following required for bootstrapping.

{% highlight bash %}
$ sudo apt-get install python-dev ncurses-dev cmake libtool
{% endhighlight %}

The following are required to build the final MMC image.

{% highlight bash %}
$ sudo apt-get install u-boot-tools util-linux mtools device-tree-compiler
{% endhighlight %}

### Downloading sources

In order to build Haiku, grab all of the following source trees.

{% highlight bash %}
$ git clone https://bitbucket.org/haikuports/haikuportergit
$ git clone https://bitbucket.org/haikuports/haikuports.git
$ git clone https://bitbucket.org/haikuports/haikuports.cross.git
$ git clone https://git.haiku-os.org/buildtools
$ git clone https://git.haiku-os.org/haiku
{% endhighlight %}

### Compile and install jam

Haiku uses a custom, community maintained fork of jam. In order to build it, run the following commands.

{% highlight bash %}
$ cd /path/haiku/buildtools/jam/
$ make
$ sudo ./jam0 install
{% endhighlight %}

By default jam is installed to /usr/local/bin. You can specify another directory to install by specifying
-sBIN_DIR=/path/to/install/jam/binary to jam0.


### Compiling Haiku

Run the following commands in the directory which contains buildtools, haiku etc cloned above. If run elsewhere,
adjust paths accordingly.

{% highlight bash %}
$ mkdir generated.arm
$ cd generated.arm
$ ../haiku/configure --build-cross-tools arm ../buildtools --include-gpl-addons \
    --include-patented-code --use-gcc-pipe --bootstrap ../haikuporter/haikuporter \
    ../haikuports.cross/ ../haikuports --target-board beagle
$ jam -q @bootstrap-mmc
{% endhighlight %}

You can pass -jX to configure script to speed up things there. I have specified upto -j4 without any issues. I don't
recommend passing -jX to jam since there can be failures.

### Compiling qemu for beagleboard-xM

As of writing, only qemu-linaro from git(not even the Launchpad PPA) supports a close target - beagleboard-xM. Here are
the steps to run the image in qemu.

{% highlight bash %}
$ git clone git://git.linaro.org/qemu/qemu-linaro.git
$ git submodule update --init
$ sudo apt-get install libglib2.0-dev
$ bash -x ./configure --target-list=arm-softmmu --python=/usr/bin/python2.7
$ make
$ sudo make install
{% endhighlight %}

If you supply --prefix directory option to configure script, you can leave out sudo depending on whether current user
can write to the specified directory.

### Running Haiku MMC in qemu

From generated.arm directory, run the following command to boot Haiku image in qemu.

{% highlight bash %}
$ qemu-system-arm -M beaglexm -drive if=sd,file=./haiku-beagle.mmc -serial stdio
{% endhighlight %}
