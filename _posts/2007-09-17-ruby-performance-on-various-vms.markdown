---
author: Bill Burcham
comments: true
date: 2007-09-17 18:36:54+00:00
layout: post
slug: ruby-performance-on-various-vms
title: Ruby Performance on Various VM’s
wordpress_id: 87
categories:
- diversion
- Ruby
---

What started a few days ago as a little tinkering with a [Google Code Jam](http://googleblog.blogspot.com/2007/09/our-plans-for-code-jam.html) practice problem (Big City Skyline) turned into a mini-benchmark of three Ruby VM's on my MacBook Pro 2.2GHz. The program (a solution to the "Big City Skyline" problem) is an O( N log( N ) ) in-memory search with a little integer math.

Quickie findings: the in-development Ruby 1.9 VM performed best -- beating 1.8.6 by a factor of two. For the numbers (comparing 1.8.6, 1.9 pre-release, and Rubinius pre-release) have a look at the [spreadsheet and chart](http://spreadsheets.google.com/pub?key=p1qlnCJRXgyIG-9uhSAnHBw) over on Google Docs. Also of note: the Ruby solution did beat the four minute time limit for N=10MM given in the [problem statement](http://services.google.com/blog_resources/Google_CodeJam_Practice.pdf). This was on random input. Worst-case performance would not be as good.

It makes me happy that the [Ruby solution](http://pastie.caboo.se/98009) had adequate performance. Also it was interesting to see how the Ruby language fit for a problem you'd usually see done in LISP. I would have liked to have dynamic scope to do a cleaner implementation of @@largest_so_far. On the other hand, "memoization" is pretty natural in an OO language like Ruby -- you create an instance (of Skyline) for each context and simply use instance variables to hold previous results.

**update** Nov 15, 2007: Reader Sumudu points out that [version 5](http://pastie.caboo.se/98009) neglects to reset the timer between pruning and non-pruning variations. [Version 6](http://pastie.caboo.se/118616) rectifies this error. With that change it's apparent that the pruning has no positive effect. In fact non-pruning actually wins by a small amount for N=1 to N=100,000 on my MacBook Pro.
