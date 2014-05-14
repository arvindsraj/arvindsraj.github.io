---
layout: post
title: "[GSoC 2014] Week #3 update"
description: "Google Summer of Code 2014: Haiku ARM port week #3"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Here’s an update on progress during week #3.

TL;DR - I didn’t succeed in creating the bootable SD card image since I’ve been preparing for exams. There are some
instructions and probably next steps in this direction listed here. More work on this will be done after exams.

<!--more-->

Since I’ve exams, I’ve not been able to create a bootable SD card image but have made some progress in this regard.
This post will contain some instructions on how to create one but it is not complete.

### Bootable SD card layout and booting overview
A bootable SD card for a beaglebone contains 2 partitions.

1. Boot partition containing u-boot related files.
2. Root partition containing the actual OS which boots up.

Since the root partition for Haiku is a BFS filesystem which isn’t understood by u-boot, an additional
haiku_loader.ub is loaded by u-boot which will then boot the BFS filesystem.

### Installing qemu for emulating beagleboard
Linaro maintains a qemu fork[1] whose objective is to improve ARM support. qemu-linaro has support for emulating a
beagleboard and a beagleboard-Xm. They also maintain a PPA[2] but that doesn’t have packages for Ubuntu 14.04 so I
chose to compile it from sources[3]. I chose beaglexm as the emulation target since the Angstrom TI GNOME Linux 2011
image[4] worked fine with it.

### Creating an image that boots u-boot
Create an empty file of size at least 70MB. I did not test smaller sizes but partition of size 20MB or less will fail
since u-boot mistakenly identifies the partition as FAT16 despite being FAT32[5].

{% highlight bash %}
$ dd if=/dev/zero of=test2.img bs=128k count=560
{% endhighlight %}

The next step is to create a partition, set it’s type to b and mark it as bootable. I’ve reproduced the shell
session below.

{% highlight bash %}
$ fdisk test2.img                                                                                                                            
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x76ba9af9.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-143359, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-143359, default 143359):
Using default value 143359

Command (m for help): t
Selected partition 1
Hex code (type L to list codes): b
Changed system type of partition 1 to b (W95 FAT32)

Command (m for help): a
Partition number (1-4): 1

Command (m for help): w
The partition table has been altered!


WARNING: If you have created or modified any DOS 6.x
partitions, please see the fdisk manual page for additional
information.
Syncing disks.
{% endhighlight %}

The next step after this is to format the partition as FAT32, mount it and copy u-boot files[6] over.

{% highlight bash %}
$ sudo losetup -o $[2048*512] --sizelimit $[143359*512] /dev/loop0 sdcard.img
{% endhighlight %}

Note that the 2048 and 143359 are respectively the first and the last sectors of the image. This becomes important
when creating an image with two partitions - specifying wrong offset and size limit can cause issues later on.

{% highlight bash %}
$ sudo mkfs.vfat -F32 /dev/loop0
$ sudo mount /dev/loop0 /mnt
$ sudo cp MLO u-boot.img uEnv.txt /mnt
$ sudo umount /mnt
$ sudo losetup -d /dev/loop0
{% endhighlight %}

### Booting u-boot from SD card image
{% highlight bash %}
$ /path/to/qemu-system-arm -M beaglexm -drive if=sd,file=./sdcard.img -clock unix -serial stdio
{% endhighlight %}
Edit: If you tried earlier and it didn't work, the qemu command was wrong. I've updated it.

qemu will run and mostly exit with the following error.
{% highlight bash %}
qemu: fatal: Trying to execute code outside RAM or ROM at 0x402f0400
{% endhighlight %}

### Next steps
The next step is to fix the jam configuration file in order to create haiku_loader.ub automatically and then generate
the haiku_loader which will hopefully boot up Haiku root partition. I’ve added the  following steps to generate the
haiku_loader manually if anyone wants to play around and give it a shot. I won’t be doing anything related to the ARM
port this week due to exams.

kallisti5 had shared a command[7] to generate the haiku_loader.ub which can be used temporarily since the jam setup is
broken.

Quoted from [7],
{% highlight bash %}
$ mkimage -A arm -O netbsd -T multi -C none -a HAIKU_BOARD_LOADER_BASE -e HAIKU_BOARD_LOADER_ENTRY_NBSD -n 'haiku_loader beagle' -d /path/to/haiku_loader  /path/to/output/haiku_loader.ub
{% endhighlight %}

where HAIKU_BOARD_LOADER_BASE   and   HAIKU_BOARD_LOADER_ENTRY_NBSD should be hex numbers matching 
http://cgit.haiku-os.org/haiku/tree/build/jam/board/beagle/BoardSetup
(the 0x80008000 numbers don't seem correct as the beaglebone's memory starts at 0x82000000)

I think it should be 0x80200000 and not 0x82000000 but I don’t know for sure. If you do make progress on this, do let
everyone know!

1. [https://launchpad.net/qemu-linaro](https://launchpad.net/qemu-linaro)
2. [https://launchpad.net/~linaro-maintainers/+archive/tools/](
    https://launchpad.net/~linaro-maintainers/+archive/tools/)
3. [http://git.linaro.org/?p=qemu/qemu-linaro.git](http://git.linaro.org/?p=qemu/qemu-linaro.git)
4. [http://beagleboard.org/latest-images](http://beagleboard.org/latest-images)
5. [https://bugs.launchpad.net/qemu-linaro/+bug/1317144](
    https://bugs.launchpad.net/qemu-linaro/+bug/1317144)
6. [http://archlinuxarm.org/os/omap/BeagleBone-bootloader.tar.gz](
    http://archlinuxarm.org/os/omap/BeagleBone-bootloader.tar.gz)
7. [http://www.freelists.org/post/haiku-gsoc/Booting-the-bootstrap-image-is-it-possible,12](
    http://www.freelists.org/post/haiku-gsoc/Booting-the-bootstrap-image-is-it-possible,12)
