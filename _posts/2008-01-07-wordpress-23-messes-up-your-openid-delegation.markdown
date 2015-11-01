---
author: Bill Burcham
comments: true
date: 2008-01-07 16:11:03+00:00
layout: post
slug: wordpress-23-messes-up-your-openid-delegation
title: WordPress 2.3 Messes Up Your OpenID Delegation
wordpress_id: 101
categories:
- identity
- OpenID
---

[![No way home](http://farm3.static.flickr.com/2003/2169624754_742036fd14_m.jpg)](http://www.flickr.com/photos/70837248@N00/2169624754/)
by [akashgoyal](http://www.flickr.com/people/70837248@N00/)

If you use your WordPress blog as an OpenID (as I do) via a link rel='openid.delegate' tag (that delegates to an external OpenID service) then you may be locked out of your accounts after upgrading to WordPress 2.3.x.

Wordpress 2.3 introduced a new feature called URL canonicalization that turns requests to foo.com into www.foo.com. The justification is that it helps normalize statistics gathering in some cases (though in my experience, Google Analytics needed no such help).

But what happens if you were using an OpenID like foo.com on a (OpenID 'consumer') site like pibb.com is that after the WP 2.3.2 upgrade you actually end up authenticating the id www.foo.com (not foo.com). So you can never get back into your foo.com account at pibb.com. Got that?

**Update 4:49pm**:

My _initial _solution was this nifty one-line [disable canonical redirects plugin](http://txfx.net/files/wordpress/disable-canonical-redirects.phps) from [Mark Jaquith](http://markjaquith.wordpress.com/2007/09/25/wordpress-23-canonical-urls/). Simply drop that in your WP plugins and enable it and you'll no longer suffer URL canonicalization. But a simpler approach was to simply set the blog URL to http://meme-rocket.com in general options. Now I'm redirecting www.meme-rocket.com to meme-rocket.com and all's well.
