---
layout: post
title: "[GSoC 2014] Week #12 update"
description: "Google Summer of Code 2014: Haiku ARM port week #12"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!

Here’s a quick update for week #12.

TL;DR - The decompression process fails because the archive is present in a location that isn’t mapped by the MMU and
subsequently allocated for the zlib output stream. No solutions have been discussed yet. It shouldn’t be long till
the kernel is running hopefully :).

<!--more-->

Last week, I started investigating why the decompression of the archive by zlib failed. Initial thoughts were that the
offsets were somehow being incorrectly passed. It took me about 2 days to understand that the offsets were indeed
correct since I was using the wrong format specified for printf(thanks jessicah for pointing that out!). Once that was
verified, I investigated the \_Inflate method again and discovered the problem was the new MMU mappings being created
when the output stream for zlib is being allocated. Some of these entries mapped the archive’s location to elsewhere
and caused incorrect data to be passed to zlib.

Now that the issue has been discovered, it should be fairly easy to fix this. I’ll wait for the inputs from the
developers on this before proceeding. I’m travelling for a good part of next week and will not be able to work on
this; thus the patch will take sometime to be merged in main tree.
