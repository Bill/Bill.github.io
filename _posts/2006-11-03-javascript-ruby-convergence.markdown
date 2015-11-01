---
author: Bill Burcham
comments: true
date: 2006-11-03 12:35:52+00:00
layout: post
slug: javascript-ruby-convergence
title: JavaScript-Ruby Convergence
wordpress_id: 33
categories:
- AJAX
- Web as Platform
---

You may have noticed that the [latest](http://www.mozilla.com/en-US/press/mozilla-2006-10-24.html) release of Firefox (2.0) supports [JavaScript](http://en.wikipedia.org/wiki/Javascript) 1.7.Â  I was curious about just what the new JavaScript features were so I did a little poking around.Â  It turns out that if you peel back a little of the arbitrarily divergent terminology you find some features that map perfectly onto Ruby.Â  JavaScript (1.6) and Ruby already had a lot in common.Â  But with JavaScript 1.7 its hard not to suspect a conscious, coordinated convergence conspiracy.

JavaScript [generators and iterators](http://developer.mozilla.org/en/docs/New_in_JavaScript_1.7#Generators_and_iterators) are now supported through the familiar (to Rubyists) **yield** keyword.Â  A generator is a function that yields values -- like a coroutine.Â  It isn't obvious how to implement the Ruby cornerstone: yield to a block passed to a method, but being able to do coroutines is a welcome advance.

Then there's this thing that JavaScript 1.7 calls [destructuring assignment](http://developer.mozilla.org/en/docs/New_in_JavaScript_1.7#Destructuring_assignment) which essentially lets you have multiple return values from a function -- just like Ruby.
On the browser front, it looks like IE7 is going to support JavaScript 1.6 and not 1.7 initially, so from a client-side perspective this is kind of academic for the moment. Â  However the convergence is hopeful from the perspective of can't-we-all-just-get-along VM sharing a la [parrot](http://www.parrotcode.org/).
