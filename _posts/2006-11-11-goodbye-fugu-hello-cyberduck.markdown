---
author: Bill Burcham
comments: true
date: 2006-11-11 18:08:34+00:00
layout: post
slug: goodbye-fugu-hello-cyberduck
title: Goodbye Fugu, Hello Cyberduck
wordpress_id: 39
categories:
- OS X
- tool
---

I've been using [Fugu](http://rsug.itd.umich.edu/software/fugu/) as my SFTP (SSH) client for a while now.Â  While I'm grateful for this free and serviceable program it is not without annoyances.Â  It's a bit clumsy at certificate-based authentication.Â  You have to specify some pretty arcane SSH command-line options (-oIdentityFile=).Â  By itself that wouldn't be so bad if Fugu didn't _forget_ those options with every restart -- forcing me to re-enter them.Â  The other shortcoming of Fugu is that it only edits with a single program - the so-called "text editor".Â  It's configurable which is good -- but there's only one of 'em.Â  What if you have multiple editors for different kinds of files?

Well I've been thinking of switching to Panic [Transmit](http://www.panic.com/transmit/) for a while.Â  The Panic apps are just beautiful and Cocoa-licious. While there is no explicit mention of SSH in the text of the Transmit page, I did notice a picture that showed SFTP configuration, so I decided to give Transmit a try.Â  Unfortunately there appears to be no way to induce Transmit to do certificate-based authentication.Â  Apparently only username/password is supported.

Enter [Cyberduck](http://cyberduck.ch/). Â  It's a Cocoa app like the other two.Â  Unlike Fugu, this one remembers settings across restarts.Â  Cyberduck even has a nice little tray that pops out with your site "bookmarks". And unlike Transmit, this one does a great job with certificate-based authentication -- going beyond Fugu's cryptic command-line escapes and letting me pick the certificate file from a nice file finder.Â  The other nice thing is that like Transmit, this one supports multiple editors.Â  Very nice.Â  Cyberduck: it takes a few more seconds to download (from [TLD](http://en.wikipedia.org/wiki/Top-level_domain) .ch = Switzerland) -- but it's definitely worth the wait.
