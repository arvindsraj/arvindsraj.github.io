---
layout: post
title: "[GSoC 2014] Week #11 update"
description: "Google Summer of Code 2014: Haiku ARM port week #11"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!

Here’s the update for week #11.

TL;DR - Haiku faults when trying to find the floppyboot archive present in stage2 loader. There seems to be a difference
in few offsets/sizes which causes different bytes to be read every time when the loader tries to detect the archive.

<!--more-->

Last week, the issues with MMU were fixed and boot progressed further. Over the weekend, the beaglebone black(sponsored
by Haiku Inc. Thank you for that!) and the additional hardware I’d ordered arrived and I spent a good part of the week
figuring out how to get the hardware connected and working(only to discover I still need to buy some more). It took a
while to get OpenOCD and serial output working. This led to the next issue - the stage 2 loader faults almost
immediately after u-boot passes control to it.

The problem is that the memory mappings for devices being used is that of the OMAP3 processors(which is what powers a
beagleboard-xM; the linaro qemu target being used), while the beaglebone black runs an AM335x processor and thus
serial output is written to the wrong location(thanks to another GSoC student, Zhuowei, for helping discover this!). In
order to fix this, a new board configuration should be created and the MMU mappings will need to be verified and
corrected if required. Since these(and any other additional work) could take considerable time, my mentor suggested
continuing working with the qemu target and take things forward.

Ithamar investigated briefly and found that there’s something wrong with the offset being used when reading the
archive from memory. Since I was focused on getting the hardware to work, I could not reproduce this issue. My goal
this week is to discover what is going wrong when trying to read the archive loaded along with the stage 2 loader.
