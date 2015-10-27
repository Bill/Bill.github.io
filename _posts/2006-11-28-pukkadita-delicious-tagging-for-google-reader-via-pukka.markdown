---
author: bburcham
comments: true
date: 2006-11-28 21:21:52+00:00
layout: post
slug: pukkadita-delicious-tagging-for-google-reader-via-pukka
title: Pukkadita = del.icio.us Tagging for Google Reader via Pukka
wordpress_id: 46
categories:
- del.icio.us
- Google Reader
- Pukka
---

Earlier this month I introduced [Gordita](http://www.memerocket.com/2006/11/05/gordita-delicious-tagging-for-google-reader/) -- a little bookmarklet generator.  The generated bookmarklet lets you del.icio.us tag not only the current web page, but more specifically the current _item_ when you're in Google Reader.  Commenter [Justin Hamilton](http://justinhamilton.org/) [asked](http://www.memerocket.com/2006/11/05/gordita-delicious-tagging-for-google-reader/#comment-708) for an enhancement: rather than invoke the del.icio.us tagging interface directly, he wanted the bookmarklet to tickle [Pukka](http://codesorcery.net/pukka) so that he could target the entry to a specific del.icio.us account (he has more than one).

Well ask and ye shall receive.  Drag**[1]** (the following hyperlink...) **[Pukkadita](url='+escape(q)+'&title='+escape(p)+'&extended='+escape(e));)** to your bookmark toolbar and then click it from any page.  Like the vanilla [Pukka bookmarklet](http://codesorcery.net/2006/03/27/pukka-is-ready-to-growl/), this one uses the current page title, location (URL) and selection (if any) to initialize the fields of a new del.icio.us entry via Pukka.  Like Gordita, though, this one is aware of Google Reader's unique DOM structure so if you're on the Google Reader page you get a reference to the current item (not the page itself).

Pukka enhancing del.icio.us tagging on your Mac, tweaked by Pukkadita to integrate seamlessly with Google Reader. Little tools working together to keep you focused. I like that!

**[1]** If you're reading this in Google Reader, the Pukkadita bookmarklet (hyperlink) above has been neutered.  It won't work.  To get a working one, navigate to the article on [memerocket.com](http://www.memerocket.com/2006/11/28/pukkadita-delicious-tagging-for-google-reader-via-pukka/) and drag your Pukkadita from there.
