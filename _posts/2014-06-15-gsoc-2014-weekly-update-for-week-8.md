---
layout: post
title: "[GSoC 2014] Week #8 update"
description: "Google Summer of Code 2014: Haiku ARM port week #8"
category: haiku
tags: [gsoc2014, haiku, arm port]
---
{% include JB/setup %}

Hey everyone!

The bootstrap-mmc target is now finally available! What this means is that you can follow all instructions in [1]
except run

{% highlight bash %}
$ jam -q @boostrap-mmc
{% endhighlight %}

instead of

{% highlight bash %}
$ jam -q @bootstrap-raw
{% endhighlight %}

and you’ll get an image that can be copied to an SD card directly.

BIG DISCLAIMER: Things are not working yet! MMU isn’t operational still and there is still a long way to go till
Haiku starts working on a beaglebone black. The good news though is if you are interested in helping out, compiling
Haiku for a beaglebone black just got much easier :).

[1] [GSoC 2014 Update for weeks 1 and 2](/haiku/2014/05/04/gsoc-2014-weekly-update-for-weeks-1-and-2)
