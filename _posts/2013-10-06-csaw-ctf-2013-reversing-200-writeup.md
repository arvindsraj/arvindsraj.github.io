---
layout: post
title: "[CSAW CTF 2013] Reversing 200 writeup"
description: "Writeup for reversing 200 challenge of CSAW CTF 2013"
category: ctf-writeups
tags: [csaw ctf 2013, reversing, ctf, writeup]
---
{% include JB/setup %}
We are given a Windows executable. Following is the most interesting portion of the
disassembly.

<!--more-->

{% highlight nasm %}
int 3
lea eax,dword ptr ds:[esi+1]
lea ecx,dword ptr ds:[eax+1]
mov dl,byte ptr ds:[eax]
inc eax
cmp dl,bl
jnz short csaw2013.0125104f
sub eax,ecx
shr eax,2
xor ecx,ecx
inc eax
cmp eax,ebx
jbe short csaw2013.0125106e
xor dword ptr ds:[esi+ecx*4],8899aabb
inc ecx
cmp ecx,eax
jb short csaw2013.01251062
push 2
{% endhighlight %}

The first instruction traps to debugger and should be noped. What follows are two loops: the first
one does some comparision and the second one probably decrypts the flag. Breaking at last instruction
in the above snippet and observing the memory location ds:\[esi+ecx\*4\] gives the flag:

flag{number2isalittlebitharder:p}
