---
author: Bill Burcham
comments: true
date: 2007-09-07 17:53:53+00:00
layout: post
slug: agnostic-unobtrusive-javascript
title: Agnostic Unobtrusive JavaScript
wordpress_id: 86
categories:
- AJAX
- RJS templates
- Ruby on Rails
---

A few weeks ago I wrote about [Unobtrusive JavaScript using the Prototype versus JQuery stacks](http://www.meme-rocket.com). In that post I came down on the side of the JQuery stack. Something I didn't analyze at the time, was: is it possible to use a little of each? For instance, say (hypothetically) that you have a big old Rails project that uses RJS all over the place and you'd like to continue using RJS -- is there a way to leverage pieces of JQuery in that scenario.

In this pursuit I learned a few things. Firstly, plain old JQuery does not "do" UJS in the following sense: if you bind a behavior to an element (via a CSS selector), that binding will not be reapplied as the DOM is manipulated. For dynamic binding, or "live" binding that gets re-applied after every DOM update, you need Brandon Aaron's [livequery](http://brandonaaron.net/docs/livequery/) plugin for JQuery. Livequery is to JQuery as Dan Webb's [Low Pro](http://www.danwebb.net/lowpro) is to Prototype -- kind of. Difference being that livequery hooks each of JQuery's DOM manipulation routines, whereas Low Pro hooks each of Prototype's Ajax.Responders "onComplete" event -- the event that happens when an [XHR](http://en.wikipedia.org/wiki/XMLHttpRequest) is complete.

What all this means is that you can use JQuery's UJS (livequery) if you use JQuery for all your DOM manipulations, or you can use Prototypes UJS (Low Pro) if you use Prototype for all your Ajax calls. The unfortunate bit is that you cannot (yet) use livequery with Prototype Ajax calls, or Low Pro with JQuery DOM manipulations. Can't we all just get along?

I wonder if a UJS package could remain stack agnostic. I think livequery is on to something by hooking DOM manipulation routines. That seems more robust (if potentially less performant) than hooking the Ajax returns. By hooking DOM manipulation  it seems that you've plugged more holes and have a more general-purpose solution. Could the livequery approach (of watching for DOM manipulation) be done in a stack-agnostic fashion rather than by hooking JQuery routines? If we could do that then we'd have a UJS package we could use no matter which JavaScript base library we choose -- or perhaps whichever [library chooses us](http://www.hogwartsprofessor.com/?p=107).
