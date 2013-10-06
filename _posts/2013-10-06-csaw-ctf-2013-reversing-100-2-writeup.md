---
layout: post
title: "[CSAW CTF 2013] Reversing 100(2) writeup"
description: "Writeup for second reversing 100 challenge of CSAW CTF 2013"
category: ctf-writeups
tags: [csaw ctf 2013, reversing, ctf, writeup]
---
{% include JB/setup %}

We are given a Windows console executable. On disassembling the executable,
<!--more-->
we see that main branches based on IsDebuggerPresent. On running the executable, the output doesn't
make sense. Loading it into the debugger gives the flag:

flag{this1isprettyeasy:)}
