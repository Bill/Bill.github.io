---
author: bburcham
comments: true
date: 2007-08-17 18:29:18+00:00
layout: post
slug: ujs-rjs-versus-pojs-prototype-stack-versus-jquery-stack
title: UJS; RJS versus POJS; Prototype Stack versus JQuery Stack
wordpress_id: 85
categories:
- AJAX
- RJS templates
- Ruby on Rails
- script.aculo.us
---

I am accustomed to using [Unobtrusive JavaScript](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript) (UJS) in my apps. UJS dynamically adds event handlers to the DOM. These event handers implement behaviors via DOM manipulations and XMLHttpRequests (XHR's). This approach is in contrast to the historical approach of specifying event handlers on HTML elements directly. The value of the UJS approach are:




	
  * it separates behavior (JavaScript) from markup (HTML) and this is good for the same reasons separating style (CSS) from markup is good

	
  * it can reduce page weight




I had been using [ujs4rails](http://www.ujs4rails.com/) but that thing is being [deprecated](http://www.danwebb.net/2007/6/16/the-state-and-future-of-the-ujs-plugin) so it isn't an option for me.  If I want to do UJS I can either use Prototype or JQuery. I don't know of any other good options.

I'm using JQuery a bit now. Pretty light use: a highlight effect here, a show/hide toggle there; some AJAX form submission. In thinking about how to proceed I'm kind of torn. I don't want to use both JQuery and Prototype longer term. For starters, that's too much mental baggage to carry around -- one must gain fluency in one or 'tother I think. Also it's a lot of page weight to load both libraries.

Another dimension is RJS vs. POJS. RJS is Rails' templating and API that lets you generate JavaScript from Ruby. POJS is "Plain Old JavaScript". If we do RJS then we almost have to stay on the Prototype stack. But with POJS a guy could go with either stack and in fact I think the scale tips to the JQuery stack for POJS as I'll show in a sec.

So here's how I see the top two options:

RJS: Prototype + Scriptaculous + RJS + Dan Webb's [Lowpro](http://www.danwebb.net/lowpro):
+ Lowpro supports UJS
+ most of us have used prototype + scriptaculous and are somewhat comfortable with it
+ native Rails (RJS) support
-  Lowpro is not as well documented (nor as widely used) as JQuery for UJS
-  I see no centralized library of plugins  (other than Scriptaculous itself) for this stack


POJS: JQuery + [MinusMOR](http://www.danwebb.net/2006/11/24/minusmor-released):
+ it supports UJS out of the box in a pretty clean way
+ people I respect seem to be moving to JQuery
+ there is a large library of [plugins](http://jquery.com/plugins/) including autocompleters and flash upload progress indicators
- very little native Rails (RJS) support -- [you can do some things](http://mad.ly/2007/05/17/jquery-ajax-rails/) but it's hard to know exactly what will work
- if you already know scriptaculous effects, you have to learn new effects


A big part of this decision hinges on the value of RJS. After [using](http://www.meme-rocket.com/category/rjs-templates/) Rails RJS templates for a year and a half or so my conclusion is that they are more trouble than they are worth. Many believe RJS is easier to write/maintain that POJS -- especially for a Ruby programmer. I believe RJS is actually a [false economy](http://en.wikipedia.org/wiki/False_economy). In real applications you really have to code in real JavaScript.

An RJS template generates JavaScript from Ruby code -- but that Ruby API is insufficiently documented (in particular with regard to the scriptaculous effects) and insufficiently capable (e.g. there is [no easy way](http://www.meme-rocket.com/2007/07/05/id-proliferation-eradication-technique-1-leverage-pageselect-with-pageinsert/) to update DOM elements matching a CSS selector through that API). Essentially a guy can waste a lot of time trying to get RJS to work for anything but the simplest demos. **So I lean toward plain-old JavaScript (POJS) instead**.

I wonder what you think. Do you place higher value on RJS for real applications? Have you found an alternate stack, or perhaps a different combination of stack elements? Must I pick one stack or the other, or is it possible and profitable to use both at once?
