---
author: Bill Burcham
comments: true
date: 2007-08-03 00:08:23+00:00
layout: post
slug: ruby-symbolto_proc-dont-forget-it
title: Ruby Symbol#to_proc — Don’t Forget It!
wordpress_id: 83
categories:
- Ruby on Rails
---

This is your code:

    
    <code>puts %w{A B}.collect{|e| e.downcase}.inspect</code>


This is my cooler code:

    
    <code>puts %w{A B}.collect( &:downcase).inspect</code>


This is how the Ruby Extension Project makes it work:

    
    <code>class Symbol
      def to_proc
        proc { |obj, *args| obj.send(self, *args) }
      end
    end</code>


This is the [explanation](http://pragdave.pragprog.com/pragdave/2005/11/symbolto_proc.html).

This is the [love](http://errtheblog.com/post/44) (see day 38).

Don't ever forget this.
