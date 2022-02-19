---
layout: post
title: "What I Learned At POMCO Today"
date: 2015-11-11
comments: true
categories: lessons coding Objective-C iOS mobile
---

### Introduction

People say you learn a lot at a good startup (even if you're at a crappy startup, you can still take the time to invest in yourself).
I wanted to start a series of blog posts chronicling what I learn, whether in the vein of technology or simple and general life skills, in
the hope that these lessons may be applicable to someone who discovers this blog on the Internet.  

My first lesson I would like to present is about..

#### The 'long' primitive in Objective-C

The ANSI C-Standard calls for a MINIMUM of [4 bytes for a long](http://stackoverflow.com/a/589684).  However it does not set a maximum.  This is important because
the iPhone 6 and 6S use a long value of 8 bytes, whereas all other iPhones use long values of 4 bytes.  So if you ever create a long that, when printed,
is the correct number on the iPhone 6 or 6S but not on any other previous generation, it's because your long is not long enough.  You need to use `long long`.

I learned about this because I was getting the Unix Epoch time and trying to convert that into time since January 1st, 1900.  So when I did the conversion on my 
iPhone 6, the `long` was able to hold the proper value.  However on any other iOS device predating the iPhone 6, this number would wrap around to negative values
because it was larger than 4 bytes could store.

So kids, remember to run your tests on all the different simulators and devices!
