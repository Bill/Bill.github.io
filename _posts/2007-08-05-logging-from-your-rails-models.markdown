---
author: bburcham
comments: true
date: 2007-08-05 16:41:49+00:00
layout: post
slug: logging-from-your-rails-models
title: Logging From Your Rails Models
wordpress_id: 84
categories:
- Ruby on Rails
---

**update 2010/10/16**: [RAILS_DEFAULT_LOGGER is deprecated](https://rails.lighthouseapp.com/projects/8994/tickets/5141-deprecationproxy-for-rails_default_logger-hides-original-warn-method). I'm using ActiveRecord::Base.logger now as in:

    
    ActiveRecord::Base.logger.debug "value of x is #{x}"


read on for the older solution…

I just spent five minutes figuring out how to log from a model class in Rails (or any class that isn't a controller or view). Y'see, there is no "logger" method on an ActiveRecord for instance.  Once again [Robby to the rescue](http://www.robbyonrails.com/articles/tag/logging) with an oblique mention of the RAILS_DEFAULT_LOGGER global. I love that guy.

Related: I wonder what [Jamis circa '05](http://ruby.jamisbuck.org/rails-injected.html#s10) was talking about when he said:


<blockquote>_Note: logging is a bad example here, because Rails already has very good support for logging. Furthermore, recent Rails releases support a _service_ keyword, for declaring and using system-global services in the same manner described here._</blockquote>


Huh? Service keyword?
