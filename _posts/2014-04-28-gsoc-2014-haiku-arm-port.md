---
layout: post
title: "[GSoC 2014] Haiku ARM port"
description: "Google Summer of Code 2014: Haiku ARM port"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

I've been selected to work on improving the Haiku ARM port. I've written in great detail about it on
[haiku-os.org](http://haiku-os.org/blog/dnivra/2014-04-24_gsoc_2014_haiku_arm_port). It is definitely going to be
a challenge and a fun 3 months. Hopefully I'll be able to complete it successfully. I've reproduced the entry below.

<!--more-->

## Current status of the ARM port
Work on the Haiku ARM was started as part of a 2010 Google Summer of Code project by pfoetchen which got the kernel
debugger working(emulating a verdex board) and displayed the Haiku splash screen on Qemu. Ithamar, kallisti5 and
puckipedia have been working on the ARM port but progress has been rather slow owing to other commitments. Currently,
Jam targets for ARM boards such as Raspberry Pi, Beaglebone and Verdex board exist but the build is broken due to
introduction of the Haiku package manager. The objective of this GSoC project is to get Haiku running on the
BeagleBone black.

## Objectives of the project
The primary objectives are

- Fix issues when trying build the ARM port by bootstrapping.
- Implement support for the ARMv7/Cortex A8 MMU.
- Fix issues that arise when booting the build(both in bootloader and kernel).
- Write memory card and video drivers for the chosen ARM board.

## Description of the project
Currently, it is possible to successfully cross compile gcc for ARM. The next step is to successfully build the ARM port
using the above cross compiled gcc by fixing all the issues that arise during the build process. This involves the
following 3 steps.

- Creating a bootstrap image of Haiku.
- Building the packages for Haiku using the bootstrap image.
- Building the complete Haiku image for ARM.

Once the build succeeds, the next step is to implement MMU support for ARM. The current MMU code is for the Verdex board
and created before MMU was standardized for ARM boards. If support for the standardized MMU were included, it would make
it easier to get Haiku running on most standard ARM boards. This will require investigation of the current status of MMU
support and if it can be used(entirely or in part). The Linux, FreeBSD and perhaps even the ChromiumOS sources will be used
as a reference for implementing MMU support.

After implementation of MMU support, I plan to fix any issues that prevent booting the kernel and executing the kernel debugger.
Once those issues have been dealt with, I plan to work on adding a software driver for the memory card. If this succeeds,
hopefully it should be possible to boot the ARM port off a memory card on the ARM board and all applications also work properly.
Once the ARM port boots up successfully, I hope to add drivers for the video. This will ensure that anyone can make full use of
Haiku to develop applications on the BeagleBone black.

## Future work of the project
The following are nice to have features which could be implemented if there is time or can be carried out as future work of the
GSoC project.

### Ethernet driver support
This was originally in the proposal but then I decided that implementing video driver and SD card driver is more important.
Networking support is definitely good but being able to boot off an SD card and show off the Haiku GUI is probably better.

### Flattened Device Tree(FDT) support to enable discovery of hardware when booting.
FDT is a data structure that contains information about the various hardware devices. It is particularly useful in embedded
systems where the hardware devices cannot be discovered like in systems with a PCI or USB bus. As the name suggests, it is a
tree of nodes which contain properties as key-value pairs. [5] has an excellent example for FDT entries in the human readable
Data Tree Source(dts) format. This is converted into the machine parseable Data Tree Binary(dtb) format using a Data Tree
Compiler(dtc). FDT is commonly used by Linux(and I think even BSDs) to discover hardware devices in architectures without
PCI support(eg: ARM).

Currently the hardware information is hardcoded into Haiku and thus the entire kernel will have to be rebuilt every time new
hardware information is added. Implementing FDT support will eliminate the need for recompilation since new hardware can be
easily discovered. FDT support is work in progress and thus the current status has to be evaluated in order to decide if it
will fit within the timeframe.

### Patches to buildtools and haiku source to allow building ARM port from within Haiku.
Currently, cross compiling the toolchain from within Haiku fails since gcc2 is default and few utilities are not yet available
for other build platforms. The goals is to contribute patches and create packages that would enable compilation of Haiku ARM
port from within Haiku.

### Comments and suggestions welcome!
I’ve read through few past blog posts and have found that lot of people are really interested in having a working ARM port for
their boards. If you have any suggestions/comments, please feel free to post here(preferred since it’ll be archived), drop by
the IRC for a chat or post comments on the Google document[7].

### References
- [Cortex-A8 MMU documentation](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.ddi0344i/I1002400.html)
- [ARMv7 architecture manual](http://www.club.cc.cmu.edu/~mjrosenb/ARM%20v7%20Architecture%20Reference%20Manual.pdf)
- [Linux boot loader](http://cache.freescale.com/files/dsp/doc/app_note/AN3980.pdf)
- [FDT Wiki](http://www.devicetree.org/Main_Page)
- [Device Tree Usage](http://www.devicetree.org/Device_Tree_Usage)
- [http://elinux.org/Device_Trees](http://elinux.org/Device_Trees)
- [Linux documentation on device trees](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/Documentation/devicetree/usage-model.txt)
- [Copy of part of the Google Summer of Code proposal submitted in Melange](https://docs.google.com/document/d/1qhvCrpTqbPJqdC9cIZkpG66m2jwR972iEvjvy_tAVHo)
