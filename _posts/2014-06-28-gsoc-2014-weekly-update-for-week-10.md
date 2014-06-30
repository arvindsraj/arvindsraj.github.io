---
layout: post
title: "[GSoC 2014] Week #10 update"
description: "Google Summer of Code 2014: Haiku ARM port week #10"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!
Here’s the update for week #10.

TL;DR - MMU is enabled and now working! I think this is good progress. Next is probably adding SD card driver support.

<!--more-->

### Status at start of the week
The bootstrap-mmc target for jam was added 2 weeks ago which greatly pushed things forward in making it easy for
others to build Haiku for beaglebone black. However, the image cannot be booted up successfully because of few
problems which will be discussed later. Before I write about the issue, I’ll briefly introduce what MMU is and
specific features of ARM’s MMU.

### Memory Management Unit
Memory Management Unit(often abbreviated as MMU) is one of the many components that enables a computer to run several
programs simultaneously. The MMU’s job is to translate the virtual addresses(what the programs, CPUs and users see)
to physical addresses(the actual memory address). Since several memory access requests arrive in quick succession, the
translation has to be extremely quick and thus the MMU is usually implemented in hardware.

### MMU unit in ARM
The main memory is usually divided into small manageable pieces called pages(to reduce fragmentation of main memory).
The MMU unit in ARM supports 4 page sizes - 16MB, 1MB, 64KB and 4KB respectively. The varying sizes enable easy
mapping of memory areas of varying sizes(eg: the entire OS code could be mapped in a single entry).

The mappings of addresses of pages is maintained in a page table. In ARMv7A, the page table has a 2 level hierarchy -
the larger sized pages(16MB and 1MB) are typically mapped from the first level into memory while the smaller sized
pages(64KB and 4KB) are translated using both levels.

### Boot process in Haiku
Before we look at the layout of Haiku in memory, here’s a quick overview of Haiku boots up into memory.

As mentioned in previous blog posts, u-boot starts up first which passes control onto haiku_loader, which understands
the BFS filesystem and thus can load the kernel into memory.

The entry point for haiku_loader is _start_netbsd[1] which passes control to _start_common[2]. Based on the value set
in r4 register, _start_common invokes start_raw or start_netbsd. For the ARM port, control passes onto start_netbsd,
which sets up few arguments which invokes start_raw. start_raw sets up a lot of things before passing control over to
the loader, which invokes the kernel.

The actions performed by start_raw are(in order)

- Initializing(clearing) bss and calling C++ constructors
- Setting up the arguments for loader’s main routine
- Initializing serial and console output
- Initialize the open firmware wrapper and CPU
- Discover uImage if passed as an argument
- Initialize MMU and enabling serial output
- Invoking loader’s main routine

### In-memory layout of Haiku
The current memory layout of Haiku is defined in LOADER_MEMORYMAP, which is declared in
src/system/boot/arch/arm/arch_mmu.cpp within the Haiku source. These virtual addresses are mapped to physical
addresses in the page table before MMU is enabled.

<table>
    <tr>
        <td><strong>Start address</strong></td>
        <td><strong>End address</strong></td>
        <td><strong>Content</strong></td>
    </tr>
    <tr>
        <td>0x80000000</td>
        <td>0x80200000</td>
        <td>Kernel</td>
    </tr>
    <tr>
        <td>0x48000000</td>
        <td>0x4A000000</td>
        <td>Devices</td>
    </tr>
    <tr>
        <td>0x80000000</td>
        <td>0x800FFFFF</td>
        <td>Loader</td>
    </tr>
    <tr>
        <td>0x80100000</td>
        <td>0x801FFFFF</td>
        <td>Page Table</td>
    </tr>
    <tr>
        <td>0x80200000</td>
        <td>0x811FFFFF</td>
        <td>Free memory</td>
    </tr>
    <tr>
        <td>0x81200000</td>
        <td>0x82000000</td>
        <td>Stack</td>
    </tr>
    <tr>
        <td>0x82000000</td>
        <td>0x82500000</td>
        <td>Initrd</td>
    </tr>
</table>

### So what’s next in the ARM port?
Over the past week, I learnt about the MMU in ARMv7 and fixed most of the issues in initializing MMU(at least for now).
The changes were merged into the Haiku source tree yesterday. Currently, it is believed that the boot process cannot
proceed because Haiku does not contain SD card driver. This will require further investigation.

- [http://cgit.haiku-os.org/haiku/tree/src/system/boot/platform/u-boot/arch/arm/shell.S#n29](
   http://cgit.haiku-os.org/haiku/tree/src/system/boot/platform/u-boot/arch/arm/shell.S#n29)
- [http://cgit.haiku-os.org/haiku/tree/src/system/boot/platform/u-boot/arch/arm/shell.S#n36](
   http://cgit.haiku-os.org/haiku/tree/src/system/boot/platform/u-boot/arch/arm/shell.S#n36)
