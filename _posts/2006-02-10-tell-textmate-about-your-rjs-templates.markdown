---
author: Bill Burcham
comments: true
date: 2006-02-10 19:38:20+00:00
layout: post
slug: tell-textmate-about-your-rjs-templates
title: Tell TextMate About Your RJS Templates
wordpress_id: 10
categories:
- AJAX
- RJS templates
- Ruby on Rails
- TextMate
---



As explanations go, it doesn't get much more straightforward than Cody Fauser's [excellent post](http://www.codyfauser.com/articles/2005/11/20/rails-rjs-templates) on Rails RJS Templates. When you try it out though, you may notice that TextMate doesn't know that RJS templates which by convention have an ".rjs" suffix, are really Ruby code. If you want to make TextMate aware of the ".rjs" suffix simply open the Bundle Editor window, navigate to the Ruby bundle, scroll down to the Ruby language definition (it has a Gray circular icon with an "L" on it), and add 'rjs' to the list of fileTypes.

![add-rjs-to-ruby-bundle1](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/add-rjs-to-ruby-bundle1.gif)

Once you do that, TextMate will treat .rjs files like other Ruby files and you'll get syntax highlighting and stuff.
