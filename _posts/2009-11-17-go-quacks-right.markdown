---
author: Bill Burcham
comments: true
date: 2009-11-17 00:20:35+00:00
layout: post
slug: go-quacks-right
title: Go Quacks Right
wordpress_id: 282
categories:
- Ruby
tags:
- duck typing
- go
- Ruby
---

In [Ruby Module#include at Odds with Duck Typing](http://memerocket.com/2006/09/28/ruby-moduleinclude-at-odds-with-duck-typing/) I lamented the fact that Ruby wouldn't treat an object as an Enumerable unless that object included the Enumerable interface explicitly.

Well Google's new Go language has no such limitation. [If it quacks, it really is a duck in Go](http://golang.org/doc/go_lang_faq.html#types):


<blockquote>Rather than requiring the programmer to declare ahead of time that two types are related, in Go a type automatically satisfies any interface that specifies a subset of its methods. Besides reducing the bookkeeping, this approach has real advantages. Types can satisfy many interfaces at once, without the complexities of traditional multiple inheritance. Interfaces can be very lightweight—having one or even zero methods in an interface can express useful concepts. Interfaces can be added after the fact if a new idea comes along or for testing—without annotating the original types. Because there are no explicit relationships between types and interfaces, there is no type hierarchy to manage or discuss.</blockquote>


Well…there it is.
