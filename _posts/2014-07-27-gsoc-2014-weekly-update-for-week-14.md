---
layout: post
title: "[GSoC 2014] Week #14 update"
description: "Google Summer of Code 2014: Haiku ARM port week #14"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hello everyone!

Here’s the update for week 14 of work on the Haiku ARM port. I was travelling for a good part of week 13 and thus
couldn’t get much work done during the time.

TL;DR - The problems of kernel overwriting the loader code and the exceptions when setting up framebuffer have been
temporarily solved. The framebuffer related code has been disabled for now and the entire memory map moved to a higher
address(when FDT support is implemented, a lot of these problems probably won’t arise). Currently, a fault is raised
when the kernel attempts to obtain the same lock twice, which drops to KDL.

<!--more-->

Over the last week, PulkoMandy and Ithamar helped me out in figuring out the problem why the kernel won’t start up -
turns out that the kernel was overwriting the loader, which prevent further loading of the kernel. This was fixed(not
yet merged into the main tree) by moving the entire memory map to a higher address. Once this was done, the next
problem was a floating point exception raised when writing to CM_CLKSEL_DSS while setting up the clocks. The register
does seem to be RW as per the OMAP35xx technical reference manual and thus I’m not sure what’s going wrong.
PulkoMandy suggested disabling the framebuffer related code for now(again the changes haven’t been merged into the
main Haiku tree).

Currently, there are two problems that arise after the fixes done above.

1. The kernel tries to lock sSpinlock twice, which leads to a panic and dropping to KDL.
2. KDL itself doesn’t work properly because the gARMPagingMethod is uninitialized for some reason and thus the call
to IsKernelPageAccessible method of the gARMPagingMethod fails. I suppose the solution is to initialize
gARMPagingMethod, which should solve the problem with KDL.
