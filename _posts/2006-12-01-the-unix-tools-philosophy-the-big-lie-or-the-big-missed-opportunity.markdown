---
author: bburcham
comments: true
date: 2006-12-01 20:15:52+00:00
layout: post
slug: the-unix-tools-philosophy-the-big-lie-or-the-big-missed-opportunity
title: 'The Unix tools philosophy: The Big Lie or The Big Missed Opportunity?'
wordpress_id: 48
categories:
- One Step Forward
---

The Unix tools philosophy is so well known and oft-repeated that it has become mantra to many.  As with so many habitual, memorized responses, like blessing someone after a sneeze, we rarely think deeply about the words said, and what they really mean.  We don't notice that while we praise this approach, we almost never use it for anything serious.

<!-- more -->


#### The Unix Philosophy


The first reference I heard of to the Unix philosophy was in the seminal 1978 Bell Systems Technical Journal special edition on Unix. (Appropriately, if you [go](http://www.lucent.com/minds/techjournal/) to the <strike>Bell Labs</strike>. <strike>Lucent</strike>, Alcatel (?) web site to subscribe to this journal, you'll find it has literally gone 404).  Again and again, this philosophy has been repeated. I've seen it most recently on Duncan Davidson's [Web is a pipe](http://blog.duncandavidson.com/2006/06/the_web_is_a_pi.html) blog entry.  Sadly, his otherwise insightful post has an example that does not demonstrate the value of pipes, but rather a [Useless use of cat](http://partmaps.org/era/unix/award.html).
The Unix philosophy, is described quite well on wikipedia, attributed to Doug McIllroy:


<blockquote>This is the Unix philosophy:

Write programs that do one thing and do it well.

Write programs to work together.

Write programs to handle text streams, because that is a universal interface.</blockquote>




#### The Big Lie


This is the Motherhood and Apple Pie of programming.  It sounds great in some idealized world, but we rarely see it in our world. Think of the Unix applications you spend the most time using every day.  For me, it is something like compiler, editor, web browser, web server, database server.  None of these are built from a large set of small tools hooked up with pipes transmitting ASCII data.  Not one single application.  Yes, I know about gcc and the -pipe option, but this doesn't count -- gcc doesn't read standard input, and the five (or so) stages of the compiler hardly qualify as small, reusable components.  Historical tidbit: In the 2BSD days, the C compiler's optimizer was implemented as an awk script that ran over the assembler input.  But now that we expect our compilers to do more than peephole optimizations, we don't do this kind of thing anymore.

Now, I use the Unix command line every day, and frequently string together shell one-liners.  I build histograms with
`sort | uniq -c | sort -n`

idiomatically.  But I don't build large applications this way.  Almost no one does. Does this mean that the Unix philosophy only applies to interactive shell programming?  Or perhaps to that and one level higher, smallish shell scripts (but certainly nothing the size of a C compiler)?  Or was the intention of the Unix designers that their machines, tiny by today's standards would mainly run small shell scripts? I would love to ask Thompson, Richie, or any of the other Bell Labs folks these questions.  It is ironic that we are **more** concerned/obsessed with performance on today's machines that are thousands of times faster and cheaper than the PDP-11s Unix was born on.


#### Solutions to the dilemma


One "solution" is to admit defeat, and say that the Unix Philosophy doesn't really work, at least not on big programs or anything more complicated than a shell one-liner.  After all, most Windows users, even power-users, never use command pipelines.  Instead of the three command pipeline above to create a histogram, a Windows power user would probably load the data up in Excel, and go from there.  Maybe that isn't as elegant, but it gets the job done just as easily as the shell command.  If this is your solution to this dilemma, I would ask you to at least **be honest** about your retreat -- if you don't build programs this way, stop proclaiming its superiority.  Please.  There are plenty of sucessful projects in the [other camp](http://www.perl.com/pub/a/1999/03/pm.html#jump4), and they are up-front about violating all three tenets.

Another solution is to claim that the problem is that the unidirectional ASCII streams aren't structured enough, and to add some more structure and state, and create a full-blown RPC mechanism.   In terms of the Unix Philosophy quoted above, we remove the third leg of the tripod (_Write programs to handle text streams, because that is a universal interface_).  Once this is done, the sin of premature optimization is inevitably commited, for there is the fear that bundling each component into a separate Unix process will lead to performance problems.  So, the word "program" is struck out of the philosophy, and replaced with the word "component", with convenient vagueness. Once these changes are made, we've pretty much given up on the validity or usefulness of the original philosophy.

This is the path that SunRPC, CORBA, DCOM, and XPCOMM take.  While each of these have been successful within limited domains, we don't see any of them used as toolkits to quickly build many, diverse applications by non-experts.  Rather, they are used to glue together predefined puzzle pieces in predefined ways.  For example, as Firefox is composed of many XPCOMM components, why can't I break apart the components, and quickly build, say, an IM client out of those components? Ho-ho, I hear you say, what about [Chatzilla](http://www.mozilla.org/projects/rt-messaging/chatzilla/)?  Chatzilla is built from over a dozen Javascript files by an expert javascript/XUL developer.  I would categorize this as an exception that proves the rule -- if it really were easy to build this up, there would be dozens of them.  Especially the first three component techologies mentioned above, make it **hard** to connect up various components, especially those which haven't been planned to interoperate _a-priori_.  The Unix philosophy stresses easy of connectability.


#### Why not try?


A wise man once told me, "If there's one thing I've learned in six years of graduate study in Computer Science, it is the value of a Unix process".  So, why not try it, and build real, large applications out of smaller unix processes connected by pipes?  There's even precedent -- the applications that [Dan Bernstein](http://cr.yp.to/djb.html) has written, like [qmail](http://www.nrg4u.com/qmail/the-big-qmail-picture-103-a4.pdf) and djbdns.  These systems, primarily designed for security, are composed of many processes which communicate via ASCII stream over pipes.  And guess what?  qmail is secure, gives good performance, and there are many add-ons and replacements for its various components.  Maybe, in this age when folks worry about how to effectively use quad-core CPUs, just maybe the [graybeards](http://www.nationalmedals.org/assets/images/Lucent.jpg) knew a thing or two after all.
