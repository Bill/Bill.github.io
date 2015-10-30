---
author: bburcham
comments: true
date: 2007-06-22 23:22:25+00:00
layout: post
slug: safari-3-beta-nav-list-eschews-anchors-in-favor-of-css-cursor-property-and-click-events
title: Safari 3 Beta Nav List Eschews Anchors in Favor of CSS cursor Property and
  Click Events
wordpress_id: 75
categories:
- CSS
- design
---

The [Safari 3 Public Beta page](http://www.apple.com/safari/) has a nice little navigation list in the middle: 12 reasons you'll love Safari. It's kind of "semantic" markup since it uses an ordered list to enumerate the choices (as opposed to e.g. a table or something). And it's kind of separating content from presentation with its use of CSS to style the list items -- including nice visual differentiation for the active (selected) item. All this is pretty standard stuff for readers of Dan Cederholm's Web Standards Solutions. And rather than reloading the whole page on each navigation click, the site is using XMLHttpRequest to load just a small section. Again, nothing surprising here.

What is a little surprising is the page's departure from the common practice of using anchor tags within the list items. Instead of each list item containing a hyperlink, it contains plain old text. Since there is no hyperlink, the browser won't provide a visual cue that the item is navigable/clickable. That's kind of ok since most designers go to pains to undo some of the cue anyway -- I'm talking about eliminating underlining. The other cue -- changing the cursor to the pointer image when mousing over the "active" list item, is implemented via a CSS rule. Then all that's left is to catch the click events in JavaScript code.  Here's the markup:

![firefoxscreensnapz033](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/firefoxscreensnapz033.jpg?w=300)

It's a pretty design and it works really nice. On the other hand it is not an example of "unobtrusive JavaScript" or of "graceful degradation". I am pointing this out not to be dogmatic about these things, rather to cite it as evidence of the difficulty of applying the aforementioned philosophies.  Here you have a lovely page, built by the best designers money can buy. It doesn't get much more high profile than this. And these guys went ahead and built a page that requires JavaScript. Made me think. I hope it makes you think too.

This feels like it's going to be a bit of a recurring theme here (see the recent [Tableful Grace](/2007/05/18/tableful-grace/) post). Graceful degradation with respect to JavaScript, CSS, heck even background images; separation of behavior from content; table-less layout -- these are great philosophies. But the practice is riddled with subtlety and compromise. And you wouldn't really know it from reading the short "gee look what I can do" type articles.

I'm curious to know how you feel about these new sacred cows now that we've had a little experience with them. For example, are you implementing whole applications with graceful degradation or only pieces of your application? If it's the latter, are you getting any real benefit? Does graceful degradation double your testing effort?
