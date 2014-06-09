---
layout: post
title: "[GSoC 2014] Weekly update for week #7"
description: "Google Summer of Code 2014: Haiku ARM port week #5"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!

Just like week 6, this is another short update.

I’ve been working on creating a bootstrap-mmc target for the build system. The current issues I’m facing are

1. Downloading MLO and u-boot fails and thus build fails since jam doesn’t know how to create those targets otherwise.
I managed to fix this but apparently, the fix breaks the current build system so I need to modify the fix such that it
doesn’t break the build system.

2. Copying the bootstrap image as the second partition of the MMC image. I planned to use dd since it works fine and
probably works fine on Linux and BSDs but dd requires a size to be specified, which is slightly tricky to compute in
jam. The obvious solution of using cat doesn’t seem to work - the resultant size of MMC is much higher than expected.

I hope to add the bootstrap-mmc target for jam within this week which would make it much easier for anyone to build the image. 
