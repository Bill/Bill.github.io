---
author: Bill Burcham
comments: true
date: 2009-03-28 23:44:06+00:00
layout: post
slug: oocss-sass-fewer-classes-on-your-elements
title: OOCSS + SASS = Fewer Classes On Your Elements
wordpress_id: 164
categories:
- CSS
- Ruby
- tool
---

**update** 3/29/2009 8:23 AM: Turns out [Chris Eppstein](http://acts-as-architect.blogspot.com/) is way ahead of me. Check out [Compass](http://compass-style.org/). It's a project that delivers your favorite [CSS frameworks](http://wiki.github.com/chriseppstein/compass/supported-frameworks) as SASS mix-ins. Oh and also it integrated with your favorite Ruby web development framework.

Stubbornella you have captivated me. I am now watching [Nicole Sullivan](http://www.stubbornella.org/content/)'s [Object Oriented CSS talk](http://www.stubbornella.org/content/2009/03/23/object-oriented-css-video-on-ydn/) for the second time. The [OOCSS framework](http://wiki.github.com/stubbornella/oocss) is a terse but ambitious thing in the tradition of [YUI Grids CSS](http://developer.yahoo.com/yui/grids/) and [Blueprint](http://www.blueprintcss.org/) but with opinions that reach beyond grids, resets and typography.

While I have certainly not mastered the OOCSS concepts I do have some early observations I'd like to share nevertheless. This of course is dangerous territory since I risk having to eat my words later. So be it. Feel free to feed ’em to me as appropriate…

It strikes me from looking at the [UML diagram](http://wiki.github.com/stubbornella/oocss/uml) for OOCSS that something is missing, or more accurately, some things are being conflated which shouldn't be. If we're thinking about objects and classes here the first question should be: what are we modeling? Well the [semantic HTML](http://en.wikipedia.org/wiki/HTML#Semantic_HTML) folks might say we need to model things like paragraphs and headings, or at another level contacts and phone numbers, or at another level sidebars and navigation. These are all semantics.

When we think about modeling these things in HTML in such as way that CSS can be profitably applied, we use HTML element types to directly model semantics where we can (p element for paragraph, h1 element for top-level heading) and we use class attributes to express the rest ('top-nav', 'address', 'phone'). If you have a look at the OOCSS UML diagram you will see in the "page layout" section a set of classes that fall into the former category ( 'doc', 'hd' for header, 'bd' for body, 'ft' for footer).

So far so good. But now how do we actually extend a class? The approach suggested in OOCSS is to change the (HTML) markup. One example from the talk is to specialize the media block class 'media' so that the fixed-size media is on the right instead of on the left. The suggestion is to define a new class 'media_ext' along with a new set of CSS rules. Then the idea is to change our HTML so that the element of interest carries two classes: 'media' and 'media_ext'. And then we define some CSS for the new media_ext module, something like this:

    
    .media_ext .fixedMedia { float:right }


This is the fundamental approach in OOCSS, so fundamental that it is on the short list of _10 Best Practices_ from slide 19 of [the talk](http://www.slideshare.net/stubbornella/object-oriented-css):


9. Extend objects by applying multiple classes to an element



The library defines a small set of base classes and a few core extensions to those. Users are invited to extend these further and we change our HTML to carry the full set of classes expressing semantics, layout and skinning too, all as separate classes.

The value in this approach is it certainly brings order to chaos. The downside is twofold: first we must pollute our HTML with layout and skinning choices and second, we must rely solely on coding conventions to ensure that each HTML element carries a self-consistent and complete set of classes.


aside: To me this feels much more akin to structured programming or maybe at best "abstract data types" than it does OO. I don't really see language-level inheritance here, nor do I see polymorphism. But that's just a terminological nit.



The first problem we need to solve is to get rid of the superfluous classes (on our elements). How could we make a media block ("fixed-width media object on the left with an open editable zone on the right") into a right-handed media block without changing our HTML? Well the classic way in CSS is to use context to distinguish this situation. So let's imagine that every odd media block ought to be right-handed. A classic approach is to have an 'odd' class on the element (or to use nth-child pseudo class). Perhaps something like this:

    
    .media.odd .fixedMedia { float:right }


Where target is the fixed-width object we'd like to be on the right-hand side instead of the left. You get the idea. The problem with this approach is that we've just coded up some potentially reusable stuff (the {float:right} part) and we've got no way to reuse it in other circumstances. Also we are now guilty of committing pitfall #1 from the talk:


1. Location dependent styles



So you can see that we are definitely violating a key tennet of OOCSS. If you're already an OOCSS dogmatist you may want to stop reading now. Still with me? OK, what if we want a right-hand media block for even rows next time? We'd have to edit that last CSS rule to look something like this:

    
    .media.even .fixedMedia { float:right }


Yuck. What's going on here? The problem is that CSS has no named/reusable/callable construct. No macro. No function. No Class. OOCSS does the best it can with plain old CSS by requiring us to define a new HTML class name. That lets us define a reusable set of CSS rules for it, but at the cost of polluting our HTML at each use.

I think a good way out of this is to break out of plain old CSS into a higher realm. Both [SASS](http://haml.hamptoncatlin.com/docs/rdoc/classes/Sass.html) and [CSS-SSC](http://shauninman.com/archive/2007/06/27/css_server_side_pre_processor) have a macro concept. SASS has its mixins and CSS-SSC has its base and based-on. What these actually add to CSS is named abstractions and reuse. So I could define a SASS mixin like this:

    
    =right-hand-media
      .fixedMedia
        float: right


Now that is a reusable module. In my application I can apply that module to an element:

    
    .media.odd
      +right-hand-media


Yay. Now I have the best of both worlds. If we have another app that wants even media items to be right-handed then we just define this:

    
    .media.even
      +right-hand-media


The value of this is that I didn't have to change my HTML markup in order to change the layout. There are examples of using the same approach to define a [clearfix module](http://wonderfullyflawed.com/2008/05/21/clearfix-as-mixin/). To those who say this approach is going to generate a lot of CSS I say, "well maybe, but I think our first priority needs to be making our source code manageable and this approach may just help us do that."

As I already pointed out, this proposal (using SASS mix-ins or CSS-SSC macros to implement reusable modules, and then using contextual CSS Rules to apply those modules) violates a core principle of OOCSS. On the upside it solves one of the wish list items from the [talk](http://www.slideshare.net/stubbornella/object-oriented-css) (on slide 60):


1. Extending objects—possible to make "sale module" inherit from "module"







    
    .module{}
    .saleModule{extends: module}


Now that approach is a bit different from the one I described for right-hand-media. But it is clear that mix-ins or macros could easily be used to reuse .module in .saleModule right? SASS mix-ins, for instance can be used (as in my first example) as straight-up mix-ins, or to implement inheritance. The choice is yours.

To close, I highly recommend watching the [Stubbornella's presentation](http://us.dl1.yimg.com/download.yahoo.com/dl/ydn/nicolesullivan.m4v). **Twice**. There is just a ton of gold in there. Meanwhile, I'll go experiment with SASS mix-ins and let you know how it works in practice and how it can be combined with some of the other principles highlighted in the talk.
