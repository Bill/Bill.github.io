---
author: bburcham
comments: true
date: 2006-02-08 22:35:03+00:00
layout: post
slug: in_place_editor_drops_paragraph_tags
title: script.aculo.us InPlaceEditor Drops Paragraph Tags
wordpress_id: 8
categories:
- AJAX
- Ruby on Rails
- script.aculo.us
---

I submitted a Rails/script.aculo.us [defect](http://dev.rubyonrails.org/ticket/3729) describing how Ajax.InPlaceEditor is stripping paragraph tags when loading the textfield/textarea.  While waiting for the Universe to respond, I've had some time to research the issue a little more.  Interestingly, the behavior seems to have originated with a [changeset](http://dev.rubyonrails.org/changeset/2282) committed last September.  That changeset actually addressed two issues: it fixed a Safari bug, and it added the convertHTMLLineBreaks functionality.

I hope the apparent [author](http://jutopia.tirsen.com/articles/2006/02/06/selenium-and-rails) of the convertHTMLLineBreaks functionality will notice this ping to his blog.  (I realize my approach for making contact is a bit unorthodox, but my feeling is that this constitutes better manners than intruding via email -- though I can't find his email address anyway).  I'd like to understand more about the original reasons for the convertHTMLineBreaks functionality.  What problems does it solve?  Why does it need to strip paragraph tags as well as line breaks.  And perhaps most importantly -- why doesn't it put something back in their place when the edited content is injected back into the DOM?
