---
layout: post
title: "[CSAW CTF 2013] Reversing 100(1) writeup"
description: "Writeup for first reversing 100 challenge of CSAW CTF 2013"
category: ctf-writeups
tags: [csaw ctf 2013, reversing, ctf, writeup]
---
{% include JB/setup %}

We are given a .Net binary. On decompiling, the following code is obtained.

<!--more-->

{% highlight vb.net %}
private static void Main(string[] args)
{
    Console.WriteLine("Greetings challenger! Step right up and try your shot at gaining the flag!");
    Console.WriteLine("You'll have to know the pascode to unlock the prize:");
    string value = Console.ReadLine();
    long num = Convert.ToInt64(value);
    long num2 = 53129566096L;
    long num3 = 65535655351L;
    if ((num ^ num2) == num3)
    {
        Console.WriteLine("yay");
    }
    else
    {
        Console.WriteLine("Incorrect, try again!");
    }
    try
    {
        byte[] iV = new byte[]
        { 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255 };
        byte[] array = new byte[16];
        BitConverter.GetBytes(num).CopyTo(array, 0);
        BitConverter.GetBytes(num).CopyTo(array, 8);
        string s = "ls/5RTxwflDrqr5G8pO9cQ1NlgQcFjcJj9x4z7oIhlfY4w42GAFqKbyzwqHAZQBZa5ctysKKWIbTgU2VxoRYohxCbPyV6sEU/tn+sIxNg6A/r5OJnIMqTs0seMrzWh5J";
        Thread.Sleep(500);
        Console.WriteLine(aClass.DecryptStringFromBytes_Aes(Convert.FromBase64String(s), array, iV));
        Console.WriteLine("Success!!");
    }
    catch (Exception arg)
    {
        Console.WriteLine("ERROR!!! darn. huh? how did I get here? Hmm, something must have gone wrong. What am I doing?", arg);
    }
    Console.WriteLine("press key to continue");
    Console.ReadKey();
}
{% endhighlight %}

So the program accepts a passcode as input and xors it with num2; the result
of the xor should be num3. Providing num2 ^ num3 as input gives the flag:

flag{I'll create a GUI interface using visual basic...see if I can track an IP address.}
