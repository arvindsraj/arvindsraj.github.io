---
layout: post
title: "Introduction to Sage"
description: "An introductory series on Sage mathematics software"
author: "Arvind"
category: sage
tags: [python, sage, mathematics, computer algebra system]
---

{% include JB/setup %}

This is the first of a series of posts on Sage, a free open source mathematics
software. <!--more--> It combines several powerful libraries such as numpy,
boost, matplotlib, pycrpto and IPython into one, making it one very handy tool.
This post will primarily deal with installing sagemath on Ubuntu and explore
some basic operations. I am assuming that you are familiar with basic Python
usage. If you are not, you should probably head to [Python
docs](http://python.org/doc/).

## Installing Sage
Sage is not available in the Ubuntu repositories as of now. However, the AIMS
team maintains a [Launchpad PPA](https://launchpad.net/~aims/+archive/sagemath)
that provides the latest version of Sagemath. The official site of
[Sagemath](http://www.sagemath.org/download.html) also provides packages for
any Linux distro.

## Sage operation modes
Sage can be used in two ways: command line or notebook interface.
1. Command line: This is same as working with the IPython shell.
2. Notebook interface: This is a web interface to Sage and is exactly same as
[Sage notebook](http://www.sagenb.org) offered online for anyone to use. The
notebook interface has built-in authentication system and allows people to
collaborate over Sage worksheets. It can be started by typing "notebook()" at
the Sage prompt.

I use the command line interface mostly and all posts will be tested on the
command line interface(I think it will work fine in the notebook interface but
I did not test it).

## IPython shell
Sage uses the IPython shell, which provides an awesome autocomplete feature
over the traditional Python shell and makes working with Sage all the more fun.
Additionally, IPython can be customized as per user preferences by adding
Python scripts to $HOME/.sage/ipython directory. Being based on Python, sage
can be combined with any other Python library to become even more cooler stuff.

## Arithmetic and built-in functions
Sage supports all general arithmetic that Python supports. The only differences
would be that '^' is the exponent function(as is the case in mathematics) while
'^^' is the XOR operator and that '/' reduces the fraction to the lowest terms
while '//' performs the integer division.

Sage has several built-in functions such as for trigonometric and arithmetic
operations. Below are some of the useful built-in operations. It is also
possible to display the result in arbitrary precision as required.
{% highlight python %}
    sage: numerical_approx(pi, prec=200)
    3.1415926535897932384626433832795028841971693993751058209749
    sage: cos(pi/4).numerical_approx()
    0.707106781186548
    sage: n(sin(pi/4))
    0.707106781186548
{% endhighlight %}

That concludes this post on Sage. Thank you for reading! Hope you follow the
rest too.
