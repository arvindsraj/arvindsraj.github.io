---
layout: post
title: "[GSoC 2014] Weekly update(for weeks #1 and #2)"
description: "Google Summer of Code 2014: Haiku ARM port week #1 #2"
category: gsoc2014, haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Here’s an update on what’s happened so far.

TL;DR - PulkoMandy managed to build the bootstrap image and kallisti5 has made some progress in running the image on
the qemu. This is a significant progress since bootstrap image build never succeeded but not quite usable on BBB.
I’ve not made much significant contribution. Instructions for building the bootstrap image are at the end.

<!--more-->

Sometime in the middle of last week, PulkoMandy managed to successfully built the bootstrap image of ARM with some
changes that can’t be committed(yet). This is significant progress since the ARM bootstrap image wouldn’t build
successfully 2 weeks ago. I’ve managed to apply those changes and build the bootstrap image successfully. The steps
for building have been specified below.

kallisti5 managed to make some progress on the booting the ARM port front but issues with floating point instructions
keep coming up which prevent progress. He has been very helpful by providing a lot of information on how to reproduce
what he’s done and also listed out what should probably be the next few steps[1].

The next step is to manually create an SD card image from which qemu can boot the haiku kernel. While this may not be
realistically possible in one week, I hope to get the bootloader working at least before I have to temporarily pause
work on this owing to exams.

### Steps to create a bootstrap image

**1. Obtain the source files**

Download the haiku source and buildtools as outlined in [2].

{% highlight bash %}
$ git clone https://git.haiku-os.org/buildtools
$ git clone https://git.haiku-os.org/haiku
{% endhighlight %}

Also download the haikuports, haikuporter and haikuports.cross sources.

{% highlight bash %}
$ git clone https://bitbucket.org/haikuports/haikuports.git
$ git clone https://bitbucket.org/haikuports/haikuporter.git
$ git clone https://bitbucket.org/haikuports/haikuports.cross.git
{% endhighlight %}

**2. Specifying existence of few packages which don’t really exist to enable building of ARM port.**

Apply the following diff to the haiku source tree

{% highlight diff %}
diff --git a/build/jam/repositories/HaikuPorts/arm b/build/jam/repositories/HaikuPorts/arm
index 6cee1a2..f255a36 100644
--- a/build/jam/repositories/HaikuPorts/arm
+++ b/build/jam/repositories/HaikuPorts/arm
@@ -11,7 +11,17 @@ RemotePackageRepository HaikuPorts
        :
        # repository architecture packages
        # primary architecture (arm)
-
+       curl-7.36.0-1
+       curl_devel-7.36.0-1
+       freetype-2.5.2-2
+       freetype_devel-2.5.2-2
+       gawk-4.1.0-2
+       icu-4.8.1.1-4
+       icu_devel-4.8.1.1-4
+       libsolv-0.3.0_haiku_2013_10_01-1
+       libsolv_devel-0.3.0_haiku_2013_10_01-1
+       zlib-1.2.8-4
+       zlib_devel-1.2.8-4
        :
 
        # source packages
{% endhighlight %}

**3. Applying patches to enable building on Debian based distros**

I use Kubuntu and had to apply the changes specified in the commit[3].

**4. Building the bootstrap image**
Run the following commands within the haiku source tree.

{% highlight bash %}
$ mkdir generated.arm
$ cd generated.arm
$ ../haiku/configure --build-cross-tools arm ../buildtools \
  --include-gpl-addons --include-patented-code --use-gcc-pipe \
  -j2 --bootstrap ../haikuporter/haikuporter ../haikuports.cross \
  ../haikuports --target-board beagle
$ jam -q @bootstrap-raw
{% endhighlight %}

If multiple processors/cores are available, use -qjX for faster compilation. Compilation can fail at times if this option is specified in which case simply drop the option and try again.

1. [http://www.freelists.org/post/haiku-gsoc/Booting-the-bootstrap-image-is-it-possible,12](
    http://www.freelists.org/post/haiku-gsoc/Booting-the-bootstrap-image-is-it-possible,12)
2. [http://haiku-os.org/guides/building/get-source-git](http://haiku-os.org/guides/building/get-source-git)
3. [http://bb.haikuports.org/haikuports.cross/commits/4287814086470ad7f6869150edf691b843aba6d8/raw/](
    http://bb.haikuports.org/haikuports.cross/commits/4287814086470ad7f6869150edf691b843aba6d8/raw/)
