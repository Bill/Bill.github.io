---
author: bburcham
comments: true
date: 2006-03-21 12:56:47+00:00
layout: post
slug: ray-ozzie-demos-web-app-clipboard
title: Ray Ozzie Demos Web App Clipboard
wordpress_id: 17
categories:
- Web as Platform
---

On March 7, in his [talk](http://conferences.oreillynet.com/cs/et2006/view/e_spkr/559) at the [O'Reilly Emerging Technology Conference](http://conferences.oreillynet.com/etech/), Ray Ozzie, CTO Microsoft, demonstrated ([short screencasts](http://spaces.msn.com/editorial/rayozzie/demo/liveclip/screencast/liveclipdemo.html), [live demo page](http://spaces.msn.com/editorial/rayozzie/demo/liveclip/liveclipsample/clipboardexample.html)) a method by which users can cut/copy/paste structured content between web applications and between web applications and Windows ones.  See also Ray's [blog post](http://spaces.msn.com/rayozzie/blog/cns%21FB3017FBB9B2E142%21285.entry?_c11_blogpart_blogpart=blogview&_c=blogpart#permalink) on the subject.  "Structured content" means things like your contact information (formatted as an [hCard](http://microformats.org/wiki/hcard)) or a photo (in [JPEG](http://www.jpeg.org/) format) or event information (in [hCalendar](http://microformats.org/wiki/hcalendar) format).  Creative DHTML developers have been supporting cut and paste and drag and drop within their applications for some time now.  Those approaches didn't span applications though. That worked fine, so long as you stayed within a single application silo.

The approach requires no proprietary technology -- instead relying solely on open standards: HTTP, XML, HTML, ECMAscript (JavaScript), DOM. This means it runs not only in IE but also in Firefox and Safari, for real, right now.  (I just tried Opera 8.52 -- no joy.)  Microsoft has released the concept and code under the [Creative Commons](http://creativecommons.org/) [Attribution-Share Alike](http://creativecommons.org/licenses/by-sa/2.5/) license. As a result I see no intellectual property hangups associated with this thing.  No [Slugworths](http://en.wikipedia.org/wiki/Mr._Slugworth) mumbling inducements for you to sell out.  No grassy knolls.  I do have a minor quibble on the IP front though: Ozzie dubs this technology "Live Clipboard" thus infecting the concept with the Microsoft "[Windows Live](http://www.live.com/)" brand.  I have been using the term "Web Application Clipboard" to describe the same concept for a [while](http://lesscode.org/2005/10/21/baby-steps-to-synergistic-web-apps/) [now](http://lesscode.org/2005/11/02/half-a-baby-step/).  I prefer a "brand free" term -- remember all the [confusion](http://www.dannyg.com/ref/javavsjavascript.html) caused by [the infection](http://www.zdnet.com.au/news/software/soa/Andreessen_PHP_succeeding_where_Java_isn_t/0,2000061733,39218171,00.htm) of JavaScript with the Java brand?

Ozzie's [Live Clipboard Technical Introduction](http://spaces.msn.com/editorial/rayozzie/demo/liveclip/liveclipsample/techPreview.html) does a good job of explaining how the demo works so I won't repeat the basics here.  After absorbing all this stuff I do have a few observations though.  First, I see no specification for the actual clipboard structure.  One of the demo screencasts shows a sample document if you're quick enough to catch it. You'll notice it's remarkably similar to the structure proposed in the lesscode article [http://www.lesscode.org/clip-source-response/sample-1](http://lesscode.org/pub/web-clip/clip-source-response-1.xml).  Ozzie's clipboard on the left, lesscode clipboard on the right:

![live-clipboard-xml-structur](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/live-clipboard-xml-structur.gif)![lesscode-clipboard-format](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/lesscode-clipboard-format.gif)



Beyond that though, I see no specification for the Microsoft clipboard structure -- no real explanation, description or constraints.  I'd like to see Microsoft provide a schema or somesuch similar to the one I built for the first Web Application Clipboard proposal [http://www.lesscode.org/clipboard/v0p1](http://lesscode.org/pub/web-clip/lesscode-org-clipboard-v0p1.xsd).  There are some important specifications embedded as annotations in that schema, for instance:





<blockquote>Domain representations come before presentational ones.  Text only representation, if present, is last.</blockquote>


The schema defines a special element to carry the optional text representation of an object on the clipboard.  If it's present at all, this element comes last.  By providing this element the vocabulary avoids the need for the dread "mixed content" (shiver). I'd also like to see Microsoft provide a prose specification for their clipboard, again from the first lesscode [article](http://lesscode.org/2005/10/21/baby-steps-to-synergistic-web-apps/):


<blockquote>The structure may contain multiple representation elements. The elements have a well-defined order so that the receiving application knows where the most presentational representations are and where the most domain specific ones are. Note that the source application may choose in the case of copy to place content in the representations either by value or by reference. The latter necessitates subsequent communication between the destination application and the source application. In the case of cut, the content is always returned by value. In the case of identify it's always returned by reference. The identify verb is for future expansion to support drag and drop gestures.</blockquote>


The Microsoft demo employs an ingenious use of a transparent input element as the hook to the native operating system clipboard.  By placing a characteristic image (a PNG of "scissors" in the demo) in the background of that element, the user is presented with a page element that when context-clicked will present cut/copy/paste options in the context menu.  The edit menu works as expected too.  When the user gestures for cut/copy/paste the corresponding ECMAscript event is raised and handled.  In the example, this is handled locally -- but there is no reason why in general this couldn't cause client-server interaction as well.

At first it seems unfortunate that we have to place a graphical indication on the page for every single thing that can be cutted or pasted.  Imagine what your Word document would look like under this regime! On the other hand, thinking of new computer users, it is entirely possible that the explicit graphical representation is sufficiently easier to understand that it's worth the clutter. Regardless, it does work, and the benefits are such that we may just have to live with it for a while.  Clutter can be addressed by various techniques such as hiding the scissors behind a hover event.

I hope that Microsoft will promote a more complete specification of the actual clipboard structure soon.  With Ray Ozzie's weight now behind the Web Application Clipboard, I expect the idea to take hold and spread.  Exciting oportunities lie ahead.
