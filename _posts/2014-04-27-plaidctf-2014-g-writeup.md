---
layout: post
title: "[PlaidCTF 2014] g++ writeup"
description: "Writeup for g++ challenge PlaidCTF 2014"
category: ctf-writeups
tags: [plaidctf 2014, reversing, c++, template meta programming]
---
{% include JB/setup %}

I certainly commend the person who designed the challenge! This is so damn obfuscated, I think it would have been very
easy to lose track of what has been implemented and what is left.

We are given a C++ source file and a makefile. Running make generates a key.h file and compiles solveme.cpp.

<!--more-->

{% highlight bash %}
$ make
echo 'guess_a_key' | perl -p -e 's/(.)/sprintf("K(%d,%d)\n",$i++,ord($1))/ge' > key.h
g++ -std=c++0x solveme.cpp -ftemplate-depth-1000000 -o solveme
{% endhighlight %}

Judging by the above, we probably have to find a key. Running the binary prints "Wrong" and exits.

{% highlight bash %}
$ ./solveme
Wrong
{% endhighlight %}

On opening the source file, we see a lot of preprocessor directives. Let's execute them all and then observe the source
code.

{% highlight bash %}
$ g++ -E solveme.cpp -o E_solveme.cpp
{% endhighlight %}

I have included the interesting portion of E_solveme.cpp below.
{% highlight cpp %}
# 1 "key.h" 1
template <> struct key<0> { static const int r = 103; };
template <> struct key<1> { static const int r = 117; };
template <> struct key<2> { static const int r = 101; };
template <> struct key<3> { static const int r = 115; };
template <> struct key<4> { static const int r = 115; };
template <> struct key<5> { static const int r = 95; };
template <> struct key<6> { static const int r = 97; };
template <> struct key<7> { static const int r = 95; };
template <> struct key<8> { static const int r = 107; };
template <> struct key<9> { static const int r = 101; };
template <> struct key<10> { static const int r = 121; };
# 59 "solveme.cpp" 2

template <int a, int b, int c>
struct m {
  static const int r = (a-((a/b)*b));
};

template <int a, int b, int c, int d>
struct n {
  static const int q = n<(a-1),((m<(b),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r)-1),(c+1),(((a+c)>>1)+(m<(d),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r))>::q ;
  static const int r = n<(a-1),((m<(b),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r)-1),(c+1),(((a+c)>>1)+(m<(d),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r))>::r ;
  static const int s = n<(a-1),((m<(b),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r)-1),(c+1),(((a+c)>>1)+(m<(d),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r))>::s ;;
};

template <int a, int c, int d>
struct n<a,0,c,d> {
  static const int r = a;
  static const int q = c;
  static const int s = d;
};

template <int s, int t, int u, int v, int w, int x, int y, int z>
struct d {
  static const int r = (m<((n<(n<(n<z,v,z,0>::s),(n<x,t,x,0>::s),(n<z,v,z,0>::s),(n<x,t,x,0>::s)>::q),(n<(n<y,u,y,0>::s),(n<w,s,w,0>::s),(n<y,u,y,0>::s),(n<w,s,w,0>::s)>::q),(n<(n<z,v,z,0>::s),(n<x,t,x,0>::s),(n<z,v,z,0>::s),(n<x,t,x,0>::s)>::q),(n<(n<y,u,y,0>::s),(n<w,s,w,0>::s),(n<y,u,y,0>::s),(n<w,s,w,0>::s)>::q)>::q)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r);
};

template <int b>
struct g {
  static const int r = ((3 + g<b-1>::r*7) ^ (g<b-1>::r*2))&255;
};

template <>
struct g<0> {
  static const int r = 13;
};

template <int f, int n>
struct u {
  static const int r = u<(((f+1)&7)?((f+1)&7):(8)),((f)>>3)>::r;
};

template <int f>
struct u<f,1> {
  static const int r = (m<((n<(n<(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),42,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),43,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),42,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),43,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),192,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),26,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),192,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),26,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),42,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),43,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),42,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),43,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),192,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),26,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),192,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),26,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q)>::q)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r)+(m<((n<(n<(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),155,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),8,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),155,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),8,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),221,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),246,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),221,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),246,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),155,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),8,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),155,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),8,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),221,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),246,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),221,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),246,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q)>::q)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r);
};

template <int f>
struct u<f,0> {
  static const int r = (m<((n<(n<(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),162,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),141,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),162,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),141,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),229,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),132,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),229,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),132,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),162,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),141,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),162,(m<((((1*f)*((1*f)*(1*f)))*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),141,(m<(((1*f)*(((1*f)*(1*f))*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),229,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),132,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),229,(m<((((1*f)*((1*f)*(1*f)))*((1*f)*((1*f)*(1*f))))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),132,(m<((((1*f)*(1*f))*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q)>::q)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r)+(m<((n<(n<(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),222,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),222,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),109,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),48,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),109,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),48,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),222,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0,(m<(((1*f)*((1*f)*(1*f)))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),222,(m<((1*f)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q),(n<(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),109,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),48,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),109,(m<(((1*f)*(1*f))),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s),(n<(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),48,(m<(1),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r),0>::s)>::q)>::q)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r);
};


template <int a, int b>
struct r {
  static const int rr = d<g<((a)<<2)>::r,g<((a)<<2)+1>::r,g<((a)<<2)+(1<<1)>::r,g<((a)<<2)+(1<<1)|1>::r,key<b>::r,key<b+1 +1 +1 +1>::r,key<b+1 +1 +1 +1 +1 +1 +1 +1>::r,key<b+1 +1 +1 +1 +1 +1 +1 +1 +1 +1 +1 +1>::r>::r;
};


template <int n>
struct gg {
  static const int r = (m<((u<n,n|2>::r)),(((1<<(1<<3))|1)),(((1<<(1<<3))|1))>::r) - r<(n>>2),((n)&3)>::rr;
};

template <int n>
struct vvv {
  static const int r = gg<n>::r|gg<n+1>::r|gg<n+2>::r|gg<n+3>::r;
};

template <int n>
struct vv {
  static const int r = vvv<0>::r|vvv<4>::r|vvv<8>::r|vvv<12>::r;
};

int main() {
  if (!vv<0>::r) {
    int i;
    char skey[] = {key<0>::r, key<1>::r, key<2>::r, key<3>::r, key<4>::r, key<5>::r, key<6>::r, key<7>::r, key<8>::r, key<9>::r, key<10>::r, key<11>::r, key<12>::r, key<13>::r, key<14>::r, key<15>::r};
    for (i = 0; i < 1<<(1<<(1<<1)); i++)
      std::cout << skey[i];
    std::cout << "\n";
  }
  else {
    std::cout << "Wrong\n";
  }
  return 0;
}
{% endhighlight %}

We see that the condition to display the key is determined by a value set at compile time. This is an example of
[template metaprogramming](https://en.wikipedia.org/wiki/Template_metaprogramming). The idea is that constants, among other
things, can be generated during compile time itself i.e. executing the code during compilation.

We immediately notice that the key is converted into an array like structure containing the ASCII values.

I rewrote almost every function except one in Python directly but will include readable C++ versions of the template
code wherever possible before including the Python code.

struct m simply computes a % b and c is simply ignored.
{% highlight c++ %}
template <int a, int b, int c>
struct m {
  static const int r = a % b;
};
{% endhighlight %}

{% highlight python %}
def m(a, b):
    return a % b
{% endhighlight %}

struct n is slightly more involved and has two cases. While the second case is straightforward, the first one is little
involved. A readable version would be as follows:
{% highlight c++ %}
template <int a, int b, int c, int d>
struct n {
  static const int q = n<(a-1),((m<(b),257,257>::r)-1),(c+1),(((a+c)>>1)+(m<(d),257,257>::r))>::q ;
  static const int r = n<(a-1),((m<(b),257,257>::r)-1),(c+1),(((a+c)>>1)+(m<(d),257,257>::r))>::r ;
  static const int s = n<(a-1),((m<(b),257,257>::r)-1),(c+1),(((a+c)>>1)+(m<(d),257,257>::r))>::s ;;
};
{% endhighlight %}

which can be simplified further as

{% highlight c++ %}
template <int a, int b, int c, int d>
struct n {
  static const int q = n<a-1,b%257 - 1,c + 1,(a+c)/2 + d%257)>::q;
  static const int r = n<a-1,b%257 - 1,c + 1,(a+c)/2 + d%257)>::r;
  static const int s = n<a-1,b%257 - 1,c + 1,(a+c)/2 + d%257)>::s;
};
{% endhighlight %}

This can be converted into an recursive function along with base case from the second struct n code.
{% highlight python %}
def n(a, b, c, d):
    if b == 0:
        return (c, a, d)
    else:
        return n((a - 1), (b % 257) - 1, (c + 1), (a + c)/2 + (d % 257))
{% endhighlight %}

The function simply decrements b, decrements a, increments c and adds (a + c)/2 to d till b becomes 0.
{% highlight python %}
def n(a, b, c, d):
    t = (((a+c)/2) * (b - 1) + d) % 257 + ((a + c)/2)
    return ((c + b % 257), (a - b % 257), t)
{% endhighlight %}

I tackled struct g next.
{% highlight c++ %}
template <int b>
struct g {
  static const int r = ((3 + g<b-1>::r*7) ^ (g<b-1>::r*2))&255;
};

template <>
struct g<0> {
  static const int r = 13;
};
{% endhighlight %}

which translates into this function
{% highlight python %}
def g(b):
    t = 13
    for i in xrange(1, (b + 1)):
        t = (3 + 7 * t) ^ (2 * t)

    return (t & 255)
{% endhighlight %}

struct d is a little difficult and I cleaned up the code a little.
{% highlight c++ %}
struct d {
static const int r =
           (m<((
              n<
                 (n<(
                     n<z,v,z,0>::s),(n<x,t,x,0>::s),(n<z,v,z,0>::s),(n<x,t,x,0>::s)
                    >::q),
                 (n<(
                     n<y,u,y,0>::s),(n<w,s,w,0>::s),(n<y,u,y,0>::s),(n<w,s,w,0>::s)
                   >::q),
                  (n<(
                     n<z,v,z,0>::s),(n<x,t,x,0>::s),(n<z,v,z,0>::s),(n<x,t,x,0>::s)
                    >::q),
                  (n<(
                     n<y,u,y,0>::s),(n<w,s,w,0>::s),(n<y,u,y,0>::s),(n<w,s,w,0>::s)
                    >::q)
               >::q)),
               257, 257
             >::r);
};
{% endhighlight %}

and rewrote in Python as a recursive function.
{% highlight python %}
def d(s, t, u, v, w, x, y, z):
  t = n(
       n(n(z, v, z, 0)[2], n(x, t, x, 0)[2], n(z, v, z, 0)[2], n(x, t, x, 0)[2])[0],
       n(n(y, u, y, 0)[2], n(w, s, w, 0)[2], n(y, u, y, 0)[2], n(w, s, w, 0)[2])[0],
       n(n(z, v, z, 0)[2], n(x, t, x, 0)[2], n(z, v, z, 0)[2], n(x, t, x, 0)[2])[0],
       n(n(y, u, y, 0)[2], n(w, s, w, 0)[2], n(y, u, y, 0)[2], n(w, s, w, 0)[2])[0]
       )[0]
  return m(t, 257, 257)
{% endhighlight %}

On substituting the values of function calls to n with the appropriate arguments, the function reduces to
{% highlight python %}
def d(s, t, u, v, w, x, y, z):
    return ((z * v) + (x * t) + (y * u) + (w * s)) % 257
{% endhighlight %}

After finish struct d, I finished off struct r quickly before turning to the monstrosity that is struct u.
{% highlight python %}
def r(a, b):
    t = a * 4
    return d(g(t), g(t + 1), g(t + 2), g(t + 3), key[b], key[b + 4], key[b + 8], key[b + 12])
{% endhighlight %}

struct u was a huge task which I don't want to write about. I'm just including the cleaned up versions of the struct
code and the corresponding Python function. I didn't bother reducing it to an iterative version.
Also, I'm not sure if the Python code is 100% accurate. I generated values I needed from the C++ source after I had
doubts about this.
{% highlight c++ %}
template <int f>
struct u<f, 0> {
(m<
    ((n<
        (n<
            (n<(m<(f*f*f*f*f*f*),257,257>::r),162,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),141,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f*f*),257,257>::r),162,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),141,(m<(f*f*f*f*f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f*f*f),257,257>::r),229,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),132,(m<(f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),229,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),132,(m<(f*f*f*f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f*f*f*f*),257,257>::r),162,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),141,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f*f*),257,257>::r),162,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),141,(m<(f*f*f*f*f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f*f*f),257,257>::r),229,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),132,(m<(f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),229,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),132,(m<(f*f*f*f),257,257>::r),0>::s)
        >::q)
    >::q)),
    257,257
>::r)
+
(m<
    ((n<
        (n<
            (n<(m<(f*f*f),257,257>::r),0,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),222,(m<(f),257,257>::r),0>::s),
            (n<(m<(f*f*f),257,257>::r),0,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),222,(m<(f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f),257,257>::r),109,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),48,(m<(1),257,257>::r),0>::s),
            (n<(m<(f*f),257,257>::r),109,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),48,(m<(1),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f),257,257>::r),0,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),222,(m<(f),257,257>::r),0>::s),
            (n<(m<(f*f*f),257,257>::r),0,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),222,(m<(f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f),257,257>::r),109,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),48,(m<(1),257,257>::r),0>::s),
            (n<(m<(f*f),257,257>::r),109,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),48,(m<(1),257,257>::r),0>::s)
        >::q)
    >::q)),
    257,257
>::r)
}
{% endhighlight %}

{% highlight c++ %}
template <int f>
struct u<f, 1> {
(m<
    ((n<
        (n<
            (n<
                (m<(f*f*f),257,257>::r),42,(m<(f*f*f),257,257>::r),0
            >::s),
            (n<
                (m<(f),257,257>::r),43,(m<(f),257,257>::r),0
            >::s),
            (n<
                (m<(f*f*f),257,257>::r),42,(m<(f*f*f),257,257>::r),0
            >::s),
            (n<
                (m<(f),257,257>::r),43,(m<(f),257,257>::r),0
            >::s)
        >::q),
        (n<
            (n<
                (m<(f*f),257,257>::r),192,(m<(f*f),257,257>::r),0
            >::s),
            (n<(m<(1),257,257>::r),26,(m<(1),257,257>::r),0>::s),
            (n<(m<(f*f),257,257>::r),192,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),26,(m<(1),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f),257,257>::r),42,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),43,(m<(f),257,257>::r),0>::s),
            (n<(m<(f*f*f),257,257>::r),42,(m<(f*f*f),257,257>::r),0>::s),
            (n<(m<(f),257,257>::r),43,(m<(f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f),257,257>::r),192,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),26,(m<(1),257,257>::r),0>::s),
            (n<(m<(f*f),257,257>::r),192,(m<(f*f),257,257>::r),0>::s),
            (n<(m<(1),257,257>::r),26,(m<(1),257,257>::r),0>::s)
        >::q)
    >::q)),
    257,257
>::r)
+
(m<
    ((n<
        (n<
            (n<(m<(f*f*f*f*f*f*),257,257>::r),155,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),8,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f*f*),257,257>::r),155,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),8,(m<(f*f*f*f*f),257,257>::r),0>::s)
    >::q),
        (n<
            (n<(m<(f*f*f*f*f),257,257>::r),221,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),246,(m<(f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),221,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),246,(m<(f*f*f*f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f*f*f*f*),257,257>::r),155,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),8,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f*f*),257,257>::r),155,(m<(f*f*f*f*f*f*),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),8,(m<(f*f*f*f*f),257,257>::r),0>::s)
        >::q),
        (n<
            (n<(m<(f*f*f*f*f),257,257>::r),221,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),246,(m<(f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f*f),257,257>::r),221,(m<(f*f*f*f*f),257,257>::r),0>::s),
            (n<(m<(f*f*f*f),257,257>::r),246,(m<(f*f*f*f),257,257>::r),0>::s)
        >::q)
    >::q))
    ,257,257
>::r)
}
{% endhighlight %}

{% highlight python %}
def u(f, t):
    if t == 0:
        t1 = pow(f, 7) % 257
        t2 = pow(f, 5) % 257
        l11 = n(t1, 162, t1, 0)[2]
        l12 = n(t2, 141, t2, 0)[2]
        l21 = n(l11, l12, l11, l12)[0]
        t1 = pow(f, 6) % 257
        t1 = pow(f, 4) % 257
        l11 = n(t1, 229, t1, 0)[2]
        l12 = n(t2, 132, t2, 0)[2]
        l22 = n(l11, l12, l11, l12)[0]
        l31 = n(l21, l22, l21, l22)[0]
        l41 = l31 % 257  # m(l31, 257, 257)

        t1 = pow(f, 3) % 257
        t2 = f % 257
        l11 = n(t1, 0, t1, 0)[2]
        l12 = n(t2, 222, t2, 0)[2]
        l21 = n(l11, l12, l11, l12)[0]
        t1 = (f * f) % 257
        t2 = 1
        l11 = n(t1, 109, t1, 0)[2]
        l12 = n(t2, 48, t2, 0)[2]
        l22 = n(l11, l12, l11, l12)[0]
        l32 = n(l21, l22, l21, l22)[0]
        l42 = l32 % 257  # m(l32, 257, 257)

        return l41 + l42
    elif t == 1:
        t1 = pow(f, 3) % 257
        t2 = f % 257
        l11 = n(t1, 42, t1, 0)[2]
        l12 = n(t2, 43, t2, 0)[2]
        l21 = n(l11, l12, l11, l12)[0]
        t1 = (f * f) % 257
        t2 = 1
        l11 = n(t1, 192, t1, 0)[2]
        l12 = n(t2, 26, t2, 0)[2]
        l22 = n(l11, l12, l11, l12)[0]
        l31 = n(l21, l22, l21, l22)[0]
        l41 = l31 % 257  # m(l31, 257, 257)
        t1 = pow(f, 7) % 257
        t2 = pow(f, 5) % 257
        l11 = n(t1, 155, t1, 0)[2]
        l12 = n(t2, 8, t2, 0)[2]
        l21 = n(l11, l21, l11, l21)[0]
        t1 = pow(f, 6) % 257
        t2 = pow(f, 4) % 257
        l11 = n(t1, 221, t1, 0)[2]
        l12 = n(t1, 246, t1, 0)[2]
        l22 = n(l11, l12, l11, l12)[0]
        l32 = n(l21, l22, l21, l22)[0]
        l42 = l32 % 257  # m(l32, 257, 257)
        return l41 + l42
    else:
        if (f + 1) & 7:
            return u((f + 1) & 7, f >> 3)
        else:
            return u(8, f >> 3)
{% endhighlight %}

The remaining functions can be easily converted.
{% highlight python %}
def gg(n):
    return (u(n, n | 2) % 257) - r(n / 4, n & 3)


def vvv(n):
    return gg(n) | gg(n + 1) | gg(n + 2) | gg(n + 3)


def vv(n):
    return vvv(0) | vvv(1) | vvv(2) | vvv(3)
{% endhighlight %}

The objective is that (u(n, n | 2) % 257) equals r(n / 4, n & 3) for all values of n from 0 to 15.

This gives 16 linear equations.

(13 * k0 + 68 * k4 + 87 * k8 + 202 * k12) % 257 = 15

(29 * k0 + 244 * k4 + 71 * k8 + 122 * k12) % 257 = 100

(173 * k0 + 228 * k4 + 247 * k8 + 42 * k12) % 257 = 162

(125 * k0 + 148 * k4 + 39 * k8 + 90 * k12) % 257 = 11


(13 * k1 + 68 * k5 + 87 * k9 + 202 * k13) % 257 = 25

(29 * k1 + 244 * k5 + 71 * k9 + 122 * k13) % 257 = 17

(173 * k1 + 228 * k5 + 247 * k9 + 42 * k13) % 257 = 71

(125 * k1 + 148 * k5 + 39 * k9 + 90 * k13) % 257 = 105


(13 * k2 + 68 * k5 + 87 * k10 + 202 * k14) % 257 = 172

(29 * k2 + 244 * k5 + 71 * k10 + 122 * k14) % 257 = 225

(173 * k2 + 228 * k5 + 247 * k10 + 42 * k14) % 257 = 187

(125 * k2 + 148 * k5 + 39 * k10 + 90 * k14) % 257 = 176


(13 * k3 + 68 * k7 + 87 * k11 + 202 * k15) % 257 = 31

(29 * k3 + 244 * k7 + 71 * k11 + 122 * k15) % 257 = 137

(173 * k3 + 228 * k7 + 247 * k11 + 42 * k15) % 257 = 191

(125 * k3 + 148 * k7 + 39 * k11 + 90 * k15) % 257 = 94

I wrote a quick script to bruteforce the possible values after failing to solve using z3py.
{% highlight python %}
def set1(k0, k4, k8, k12):
    if (13 * k0 + 68 * k4 + 87 * k8 + 202 * k12) % 257 != 15:
        return False

    if (29 * k0 + 244 * k4 + 71 * k8 + 122 * k12) % 257 != 100:
        return False

    if (173 * k0 + 228 * k4 + 247 * k8 + 42 * k12) % 257 != 162:
        return False

    if (125 * k0 + 148 * k4 + 39 * k8 + 90 * k12) % 257 != 11:
        return False

    return True


def set2(k1, k5, k9, k13):
    if (13 * k1 + 68 * k5 + 87 * k9 + 202 * k13) % 257 != 25:
        return False

    if (29 * k1 + 244 * k5 + 71 * k9 + 122 * k13) % 257 != 17:
        return False

    if (173 * k1 + 228 * k5 + 247 * k9 + 42 * k13) % 257 != 71:
        return False

    if (125 * k1 + 148 * k5 + 39 * k9 + 90 * k13) % 257 != 105:
        return False

    return True


def set3(k2, k5, k10, k14):
    if (13 * k2 + 68 * k5 + 87 * k10 + 202 * k14) % 257 != 172:
        return False

    if (29 * k2 + 244 * k5 + 71 * k10 + 122 * k14) % 257 != 225:
        return False

    if (173 * k2 + 228 * k5 + 247 * k10 + 42 * k14) % 257 != 187:
        return False

    if (125 * k2 + 148 * k5 + 39 * k10 + 90 * k14) % 257 != 176:
        return False

    return True


def set4(k3, k7, k11, k15):
    if (13 * k3 + 68 * k7 + 87 * k11 + 202 * k15) % 257 != 31:
        return False

    if (29 * k3 + 244 * k7 + 71 * k11 + 122 * k15) % 257 != 137:
        return False

    if (173 * k3 + 228 * k7 + 247 * k11 + 42 * k15) % 257 != 191:
        return False

    if (125 * k3 + 148 * k7 + 39 * k11 + 90 * k15) % 257 != 94:
        return False

    return True


def brute():
    key = [0] * 16

    solve1 = False
    solve2 = False
    solve3 = False
    solve4 = False
    for i in xrange(32, 0x7e):
        for j in xrange(32, 0x7e):
            for k in xrange(32, 0x7e):
                for l in xrange(32, 0x7e):
                    if not solve1:
                        if set1(i, j, k, l):
                            key[0] = i
                            key[4] = j
                            key[8] = k
                            key[12] = l
                            solve1 = True
                    if not solve2:
                        if set2(i, j, k, l):
                            key[1] = i
                            key[5] = j
                            key[9] = k
                            key[13] = l
                            solve2 = True
                    if not solve3:
                        if set3(i, j, k, l):
                            key[2] = i
                            key[6] = j
                            key[10] = k
                            key[14] = l
                            solve3 = True
                    if not solve4:
                        if set4(i, j, k, l):
                            key[3] = i
                            key[7] = j
                            key[11] = k
                            key[15] = l
                            solve4 = True

                    if solve1 and solve2 and solve3 and solve4:
                        return key

if __name__ == "__main__":
    key = brute()
    print ''.join(map(chr, key))
{% endhighlight %}

{% highlight bash %}
$ time python brute.py                                                                                                                                     
C++_m0re_lyk_C--
python brute.py  25.46s user 0.02s system 100% cpu 25.475 total
{% endhighlight %}
