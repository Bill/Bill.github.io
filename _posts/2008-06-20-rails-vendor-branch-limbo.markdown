---
author: bburcham
comments: true
date: 2008-06-20 20:28:33+00:00
layout: post
slug: rails-vendor-branch-limbo
title: Rails Vendor Branch Limbo
wordpress_id: 106
categories:
- One Step Forward
- Ruby on Rails
---

[![Stick Figure (6)](http://farm3.static.flickr.com/2307/2482546331_130e605362_m.jpg)](http://www.flickr.com/photos/cwalker71/2482546331/)I'm upgrading a project from Rails 2.0.2 to Rails 2.1.   


  
This thing uses Comatose 0.8.1. Unfortunately, Comatose 0.8.1 isn't compatible with Rails 2.1. Fine, I'll just upgrade to [Comatose 2.0 (uber-alpha)](http://github.com/darthapo/comatose/tree/master) and that'll work. Oops, Comatose 2.0 uber-alpha [breaks Rails migrations](http://groups.google.com/group/comatose-plugin/browse_thread/thread/e6402c29de92edc7). Fixing that breakage requires a [patch to Rails itself](http://pastie.textmate.org/181667).  
  
Oh and did I mention that this project of mine also requires a [patch to Comatose](http://code.google.com/p/comatose-plugin/issues/detail?id=20&q=before_filters#makechanges) proper (adds before_filters to the Comatose configuration object).  
  
So I need a patched version of Comatose and a patched version of Rails. “OK” you say, just use [Piston](http://piston.rubyforge.org/) to manage those vendor branches. Not so fast. Piston only works with Subversion and the Rails project is no longer hosted on a Subversion repository. The [old repository](http://dev.rubyonrails.org/svn/rails/tags/) was [deprecated](http://dev.rubyonrails.org/svn/rails/tags/) after Rails 2.0.2. Rails is now hosted on Github. Oh and so is Comatose.  
  
Never fear, Github support for Piston is coming [Real Soon Now](http://blog.teksol.info/2008/3/15/piston-2-0-progress-piston-can-import-all-four-cases)™. Until then I'm stuck in limbo. I suppose I'll do the [manual vendor branch](http://lookfirst.com/2007/11/subversion-vendor-branches-howto.html) thing—essentially manage my own private Subversion repositories for Rails and Comatose.  
  
The fact that [François Beausoleil](http://blog.teksol.info/) is implementing Piston support for all-Git projects leads me to believe that there is no convenient alternative (to Piston) for vendor branches in Git. Hum, that's hard to believe. Anyhow, I can't migrate this project to Git yet so all-Git alternatives are sort of moot.
