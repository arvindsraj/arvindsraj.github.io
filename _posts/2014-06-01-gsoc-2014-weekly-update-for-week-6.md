---
layout: post
title: "[GSoC 2014] Weekly update for week #6"
description: "Google Summer of Code 2014: Haiku ARM port week #5"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!

Here’s an update on week 6(a relatively short one so no TL;DR :) ).

I managed to fix the bootable SD card image generation script that I created last week to use mtools and thus eliminate
the need for losetup and root permissions. The script is available in a Github[1] repository.

Meanwhile, I’ve also been working on adding a bootstrap-mmc target to Jam. Right now, the target generates the
haiku_loader_nbsd.ub successfully but fails because it doesn’t know how to generate MLO and u-boot. I have not
figured out why it doesn’t know how to make targets MLO and u-boot. Hopefully, I’ll fix that up in a few days.

### References

[1] [https://github.com/arvindsraj/haiku-sdcard-image-generator](https://github.com/arvindsraj/haiku-sdcard-image-generator)
