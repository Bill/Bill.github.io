---
author: bburcham
comments: true
date: 2006-05-06 12:30:02+00:00
layout: post
slug: textmate-renders-styled-html-now
title: TextMate Renders Styled HTML Now
wordpress_id: 20
categories:
- TextMate
---

I use [TextMate](http://macromates.com/) and just love it.  I particularly like the syntax highlighting and formatting and the ability to pick themes.  My current favorite is Twilight.

The way TextMate understands various languages (e.g. HTML, CSS, Ruby) is via "bundles".  Bundles also define extra "commands" to the editor e.g. there's a command to run the W3C syntax validator on the current document. There are a bunch of bundles that ship with TextMate, and there are also a bunch being developed by the community.

Anyhoo, over on #refreshdallas IRC yesterday evening I was asking if anyone knew of a way to induce TextMate to output a styled HTML file for whatever file you were currently editing.  The idea was that if I was editing Ruby, or CSS, or HTML or whatever, and I'd chosen a particular theme, and I liked the way that looked -- I'd like to be able to press a button and generate an HTML document that I could drop into my blog or into an S5 presentation.  The way I've been doing this lately is to capture a TIFF of the TextMate window via Grab but that's just tedious (have to convert to GIF and then there are all the sizing limitations associated with images.)

While I was on the #refreshdallas IRC I was also on the ##textmate IRC asking similar questions and some [really](http://subtlegradient.com) [helpful](http://macromates.com/blog/) people told me about the "[Experimental](http://macromates.com/svn/Bundles/trunk/Bundles/Experimental.tmbundle/)" bundle.  It had a command very close to what I needed called "View Document as HTML".  That one did not use the current theme to generate the CSS (it had a hard-coded CSS embedded) and it didn't show the HTML -- it rendered to a WebKit HTML window directly.  They suggested I use that command as a starting point, so I played around w/ it a bit and made a little progress.

And then I watched [a movie](http://www.scifi.com/firefly/episodes/season1/0102/) and went to sleep...

And when I woke up this am, ready to go do this thing, I decided to start clean and do a fresh "svn co" on the Experimental bundle before getting down to the business of writing a TextMate-theme-to-CSS transformation (XSLT) and wrapping that in a TextMate command and mashing it up with the "View Document as HTML" genetic material.  When lo I noticed in the Experimental/Commands a new entry: "Create HTML from Document / Selection".

So I ran it.

Up popped a new editor window containing an HTML document with loads of delicious CSS and the target document rendered in the body all formatted nice w/ spans w/ class attrs.

I hit command-B to render that file in the browser and pow!  Up popped a Firefox window that looked very much like the original TextMate window!  The layout was great.  The colors perfect.  The font was a little off, but hey, that can be tweaked.

So what elf was responsible for delivering this overnight gift?  A bit of poking around on ##textmate revealed it was a certain [Brad Choate](http://bradchoate.com) had contributed this blessed addition.  Now I'm off to plumb the sublime depths of his implementation.
