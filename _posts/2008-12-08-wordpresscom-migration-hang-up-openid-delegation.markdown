---
author: bburcham
comments: true
date: 2008-12-08 18:24:40+00:00
layout: post
slug: wordpresscom-migration-hang-up-openid-delegation
title: 'wordpress.com Migration Hang-up: OpenID Delegation'
wordpress_id: 129
categories:
- OpenID
---

The first issue I've run into with my [migration](/2008/12/05/home-again/) from wordpress.org (self-hosted) to wordpress.com is that I see no way to insert [OpenID delegation](http://wiki.openid.net/Delegation) links into my template.

<!-- more -->

See I've [been using](/2008/01/07/wordpress-23-messes-up-your-openid-delegation/) http://meme-rocket.com as my OpenID. By ensuring that my template had appropriate link elements, I was able to delegate actual OpenID authentication to JanRain's myopenid.com provider. The idea was that rather than using my JanRain-issued OpenID everywhere, I'd use http://meme-rocket.com and then I'd be free to change providers at a later date if needed.

Since moving to wordpress.com I've found no way to add the necessary links to the template. Only [answer I got](http://en.forums.wordpress.com/topic/add-openid-delegation-link-to-head?replies=3#post-268176) on the support forum was:


<blockquote>Sorry, I don't think you're in the right place…</blockquote>


I get that a lot.

If I can't get the folks at wordpress.com support to solve this it'll mean I won't be able to use my blog URL as my OpenID any more. That will be a little sad. Also It'll entail groveling around to a bunch of sites and updating profiles. Worst case I may have to temporarily redirect my domain back to my (self-hosted) blog in order to authenticate (under my old OpenID) in order to change to a new one

**update 15:19: **well while it's true that there is no way to insert those links, since wordpress.com is an OpenID provider I don't strictly need the links in order for my blog URL to serve as my OpenID. I just verified that I am able to enter http://meme-rocket.com into relying party sites and that they are able to authenticate me. Minor issue that remains is that since I'm redirecting (DNS) meme-rocket.com to memerocket.com I actually get authenticated under the latter domain. So if I really want my accounts back I'll have to temporarily un-redirect that domain and then log in to all the affected accounts and change them to use memerocket.com.
