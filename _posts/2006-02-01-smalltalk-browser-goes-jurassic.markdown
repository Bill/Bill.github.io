---
author: bburcham
comments: true
date: 2006-02-01 13:14:19+00:00
layout: post
slug: smalltalk-browser-goes-jurassic
title: Smalltalk Browser Goes Jurassic
wordpress_id: 5
categories:
- AJAX
- One Step Forward
- Seaside
- Smalltalk
- Squeak
- Web as Platform
---

[dabbledb](http://smallthought.com/clips/lispvan.mov) is one of the two coolest web apps I've seen in the past month (more on the other in an upcoming post).  It's just amazing and it wouldn't be possible without [Squeak](http://www.squeak.org/) -- a robust, free Smalltalk envirionment, and [Seaside](http://www.seaside.st/) -- a state of the art web application framework. It's easy to look at the Seaside [tutorials](http://www.seaside.st/Videos/) though and think, after the initial buzz has worn off, "wow that was cool, but goodness -- Squeak's 80's UI has got to go." Don't get me wrong -- the _functionality_ is awesome, but oh my goodness.

But hold the phone.  If you look at the [Seaside tools](http://www.netstyle.ch/seaside/videos/107-SeasideTools.mov) you see a demonstration of Seaside's [LiveWeb](http://squeak.saltypickle.com/LiveWeb/).  LiveWeb is a lot like the traditional Smalltalk browser but rendered, well, in your _browser_.  So you're chunking along in your Seaside application and you decide you need to debug something, and rather than using Squeak's browser, you hit the "toggle halos" link on the current page and ka-ching, the page view is transformed.  Page components sprout frames and icons enabling you to switch between a rendered view, an HTML (source) view and a Smalltalk object view.  There's also access to a full-fledged Smalltalk object browser with live source editing.

Smalltalk's browser paradigm will not be bound to an aesthetically-impaired user interface technology.  Like Crichton's Raptor, it has found a way out.   It has broken free into the (web) browser.  For now, this has resulted only in a marginal usability improvement -- LiveWeb has about the same Aesthetics as Squeak's browser.  But how long will it be before LiveWeb gets its face lift?  If [Avi Bryant](http://smallthought.com/avi/) and friends can create a web application as visually striking as dabbledb, it's only a matter of time before they, or someone else applies the same level of care to LiveWeb.

How long will it be before a complete IDE is delivered as a web application?  To varying degrees, [Eclipse](http://www.eclipse.org/) and [IntelliJ IDEA](http://www.jetbrains.com/idea/) are stuck on the same island that Smalltalk was.  They're all trying to be graphically rich and run on many platforms.  They're all expending lots of resources maintaining UI toolkits (think of Eclipse's [Rich Client Platform](http://wiki.eclipse.org/index.php/Rich_Client_Platform)).  And the resultant UI technology, while often innovative and sometimes pleasing, suffers a "credibility gap" when compared with platform-specific technology on the Mac or Windows.  When will the IDE's throw their weight behind the DHTML+AJAX crowd and embrace the "third platform"?
