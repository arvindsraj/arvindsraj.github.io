---
layout: post
title: "[CSAW CTF 2013] Reversing 300 writeup"
description: "Writeup for reversing 300 challenge of CSAW CTF 2013"
category: ctf-writeups
tags: [csaw ctf 2013, reversing, ctf, writeup]
---
{% include JB/setup %}

We are given a 32 bit Linux ELF binary. On disassembling it, we see that
<!--more-->
it creates a forking server that listens on a random port. On accepting a
connection, the handler executes the following code:
{% highlight nasm %}
mov     eax, [ebp+name]
mov     [esp], eax
call    sub_8048B9F
mov     eax, [ebp+fd]
mov     [esp], eax
call    close
mov     dword ptr [esp], 10h
call    alarm
{% endhighlight %}

The operations achieve the following:
1. Drop privileges to another user change working directory.
2. Sets an alarm for 16s. This causes the handler to exit after the duration.

I chose to patch these operations to make things easier.

After doing the above, the actual handler function that performs the key
validation is executed. The following is the C code of the key validation
routine. The expected return value is 0xEF2E3558.

{% highlight c %}
int validate_key(char *input_string)
{
  char curr_char, *temp;
  int ret_val, temp_var;

  curr_char = input_string[0];
  temp = input_string;
  ret_val = 1337;
  if (temp != NULL)
  {
    do
    {
      temp_var = 32 * ret_val + curr_char;
      curr_char = *temp;
      temp++;
      ret_val += temp_var;
    }
    while ( curr_char );
  }
  return ret_val;
}
{% endhighlight %}

I wrote a Python script to obtain the correct key. The following script already knows
the first 5 characters and finds the last character. The 5 chars can be found by following
an approach similar to the code in first few commented lines.

{% highlight python %}
#!/usr/bin/python

"""
for i in range(33, 127):
    value = func2(chr(i) * 6)
    if value >> 28 == 0xe:
        print i, hex(value)

"""

def func2(a):
    ret_val = 1337
    for i in a:
        ret_val = (33 * ret_val + ord(i)) & 0xffffffff

    return ret_val

if __name__ == "__main__":
    req = 0xEF2E3558

key = "`r" + chr(129) + chr(119) + chr(94)

for i in range(33, 256):
    value = func2(key + chr(i))
    if value == req:
        key += chr(i)

print map(ord, key)
{% endhighlight %}

Sending the string found gives the output containing the flag:
Flag: day 145: they still do not realize this software sucks
