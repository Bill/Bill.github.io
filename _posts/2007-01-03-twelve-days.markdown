---
author: bburcham
comments: true
date: 2007-01-03 03:34:11+00:00
layout: post
slug: twelve-days
title: Twelve Days
wordpress_id: 56
categories:
- Crossfit
- diversion
---

I've been [enjoying](http://crossfitplano.typepad.com/crossfit_plano/2006/11/tuesday_1128200.html) CrossFit for a few months now through [Crossfit Plano](http://www.crossfitplano.com/).  Many of the workouts have names -- like "Fran" or "Cindy".  It's often kind of like circuit training where you're trying to work your way up to some prescribed weight with good form while minimizing the elapsed time.  The workouts are basically as extreme as you want them to be.  There is definitely something for everyone.

So I walk into class last Saturday and on the whiteboard is "[The Twelve Days of Crossfit](http://www.crossfit.com/cf-affiliates/2006/12/sunday_061224.html)".  It's like the song but you get to give yourself gifts -- like handstands and clean-and-jerks.  And you don't wait a day between sets -- you try to do the whole thing as fast as you can.  Anyway I didn't do the workout myself but I was intrigued by the sheer number of repetitions involved.  Many of us have sung the song -- but I wondered just how many "gifts" (repetitions) there were.  A trip to Wolfram MathWorld (see [Power Sums](http://mathworld.wolfram.com/PowerSum.html)) and a little noodling yielded the following...

On each "day" (j) we perform j repetitions so the number of reps per day is:

$latex (1) \sum_{j=1}^{i}{j}$

And we do that for a number of days (N) so the total number of reps is:

$latex (2) \sum_{i=1}^{N}{\sum_{j=1}^{i}{j}}$

But Wolfram MathWorld tells us that the inner sum can be reduced:

$latex (3) \sum_{j=1}^{i}{j} = \frac{1}{2}i(i+1)$

So the total from (2) becomes:

$latex (4) \sum_{i=1}^{N}{\frac{1}{2}i(i+1)}$

Which is the same as this:

$latex (5) \sum_{i=1}^{N}{\frac{1}{2}i^2 + \frac{1}{2}i}$

We know (again from MathWorld) that:

$latex (6) \sum_{i=1}^{N}{i^2} = \frac{1}{6}(2n^3 + 3n^2 + n)$

Applying (6) and (3) to (5) gives the final solution to (2):

$latex (7) \sum_{i=1}^{N}{\sum_{j=1}^{i}{j}} = \frac{1}{6}n^3 + \frac{1}{2}n^2 + \frac{1}{3}n$

For N = 12 (Twelve Days of CrossFit) we perform a total of 364 reps! Thank goodness there aren't 13 days or we'd have to do 454.

I'm sure my coach would be more impressed if I could do a proper squat.  On the other hand, one must keep oneself entertained.
