---
layout: post
title: "[GSoC 2014] Week #5 update"
description: "Google Summer of Code 2014: Haiku ARM port week #5"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!
Here’s an update on week #5.

TL;DR - Created a bootable SD card image that works when emulating BeagleBoard-xM in qemu. Control passes over to
haiku_loader, which then crashes. A link to the necessary u-boot files and an sh compatible shell script that
generates the SD card image is provided at the end.

<!--more-->

Over the last week, I managed to generate an SD card image which starts u-boot and passes control over to haiku_loader,
which then crashes. This post will include instructions on how to create the haiku_loader and thus the bootable SD
card image.

### Creating haiku_loader
As mentioned in the [week #3 post](/haiku/2014/05/12/gsoc-2014-weekly-update-for-week-3), the haiku_loader loads Haiku
contained in the BFS filesystem. The command to generate haiku_loader which starts at 0x80008000 and has entry point
as 0x80008008 is

{% highlight bash %}
$ mkimage -A arm -O netbsd -T multi -C none -a 0x80008000 -e 0x80008008 \ 
  -n 'haiku_loader beagle' -d objects/haiku/arm/release/system/boot/haiku_loader \
  ./haiku_loader_nbsd_beaglexm.ub
{% endhighlight %}

### Creating the SD card image
These are the list of steps to generate the SD card. I highly recommend using the script in the 
[archive](https://drive.google.com/file/d/0B3_ne2_7uinUbF9rMHFJczlYMEU/edit?usp=sha) to create the SD card image.

1. Generate the ARM bootstrap image.
2. Create an SD card image of size 512MB(at least 70MB larger than size of haiku bootstrap image generated using jam)
   with 2 partitions.
3. The first one should be 70MB(more is fine) in size as describe in [1]. Copy the u-boot files in the
   [archive](https://drive.google.com/file/d/0B3_ne2_7uinUbF9rMHFJczlYMEU/edit?usp=sharing) to the first partition as
   well as the haiku_loader_nbsd.ub generated above.
4. dd the bootstrap image into the second partition on the image.
5. Run the following command

{% highlight bash %}
$ qemu-system-arm -M beaglexm -drive if=sd,file=./sdcard.img \
  -clock unix -serial stdio -s
{% endhighlight %}

which should produce output similar to this.

{% highlight bash %}
VNC server running on `127.0.0.1:5900'

U-Boot SPL 2011.12-00006-gb1fd369 (Dec 30 2011 - 09:48:45)
Texas Instruments Revision detection unimplemented
OMAP SD/MMC: 0
reading u-boot.img
reading u-boot.img


U-Boot 2011.12-00006-gb1fd369 (Dec 30 2011 - 09:48:45)

OMAP36XX/37XX-GP ES1.1, CPU-OPP2, L3-165MHz, Max CPU Clock 1 Ghz
OMAP3 Beagle board + LPDDR/NAND
I2C:   ready
DRAM:  512 MiB
NAND:  256 MiB
MMC:   OMAP SD/MMC: 0
*** Warning - bad CRC, using default environment

ERROR : Unsupport USB mode
Check that mini-B USB cable is attached to the device
In:    serial
Out:   serial
Err:   serial
Beagle xM Rev A
No EEPROM on expansion board
No EEPROM on expansion board
Die ID #51454d5551454d555400000051454d55
Net:   Net Initialization Skipped
No ethernet found.
Hit any key to stop autoboot:  0
The user button is currently NOT pressed.
SD/MMC found on device 0
reading uEnv.txt

118 bytes read
Loaded environment from uEnv.txt
Importing environment from mmc ...
Running uenvcmd ...
reading haiku_loader_nbsd.ub

284804 bytes read
## Booting kernel from Legacy Image at 80008000 ...
   Image Name:   haiku_loader beagle
   Image Type:   ARM NetBSD Multi-File Image (uncompressed)
   Data Size:    284740 Bytes = 278.1 KiB
   Load Address: 80008000
   Entry Point:  80008008
   Contents:
      Image 0: 284732 Bytes = 278.1 KiB
   Verifying Checksum ... OK
   XIP Multi-File Image ... OK
OK
## Transferring control to NetBSD stage-2 loader (at address 80008008) ...
qemu: fatal: Trying to execute code outside RAM or ROM at 0xe59f300c

R00=8004d83c R01=00000000 R02=8003c328 R03=e59f300c
R04=00000000 R05=00000002 R06=f097e09d R07=9ff097e0
R08=9ff04f68 R09=fffffff0 R10=9ffabc88 R11=9ff04b14
R12=9ff04b18 R13=9ff04b00 R14=8000817c R15=e59f300c
PSR=800001d3 N--- A svc32
s00=00000000 s01=00000000 d00=0000000000000000
s02=00000000 s03=00000000 d01=0000000000000000
s04=00000000 s05=00000000 d02=0000000000000000
s06=00000000 s07=00000000 d03=0000000000000000
s08=00000000 s09=00000000 d04=0000000000000000
s10=00000000 s11=00000000 d05=0000000000000000
s12=00000000 s13=00000000 d06=0000000000000000
s14=00000000 s15=00000000 d07=0000000000000000
s16=00000000 s17=00000000 d08=0000000000000000
s18=00000000 s19=00000000 d09=0000000000000000
s20=00000000 s21=00000000 d10=0000000000000000
s22=00000000 s23=00000000 d11=0000000000000000
s24=00000000 s25=00000000 d12=0000000000000000
s26=00000000 s27=00000000 d13=0000000000000000
s28=00000000 s29=00000000 d14=0000000000000000
s30=00000000 s31=00000000 d15=0000000000000000
s32=00000000 s33=00000000 d16=0000000000000000
s34=00000000 s35=00000000 d17=0000000000000000
s36=00000000 s37=00000000 d18=0000000000000000
s38=00000000 s39=00000000 d19=0000000000000000
s40=00000000 s41=00000000 d20=0000000000000000
s42=00000000 s43=00000000 d21=0000000000000000
s44=00000000 s45=00000000 d22=0000000000000000
s46=00000000 s47=00000000 d23=0000000000000000
s48=00000000 s49=00000000 d24=0000000000000000
s50=00000000 s51=00000000 d25=0000000000000000
s52=00000000 s53=00000000 d26=0000000000000000
s54=00000000 s55=00000000 d27=0000000000000000
s56=00000000 s57=00000000 d28=0000000000000000
s58=00000000 s59=00000000 d29=0000000000000000
s60=00000000 s61=00000000 d30=0000000000000000
s62=00000000 s63=00000000 d31=0000000000000000
FPSCR: 00000000
[1]    8452 abort (core dumped)  qemu-system-arm -M beaglexm -drive if=sd,file=./sdcard.img -clock unix -seria
{% endhighlight %}

### Next steps
The next steps are
- Fix the out of bounds code execution at 0xe59f300c(which might take a while).
- Fix the build system so that it can automatically generate the SD card image.

If you’re planning to debug the issue, I recommend using gdb-multiarch and binutils-multiarch to enable remote
debugging/disassembly ARM binaries on non-ARM systems.
