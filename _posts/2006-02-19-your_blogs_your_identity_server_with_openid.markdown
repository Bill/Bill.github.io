---
author: bburcham
comments: true
date: 2006-02-19 14:50:39+00:00
layout: post
slug: your_blogs_your_identity_server_with_openid
title: Your Blog’s Your Identity Server With OpenID
wordpress_id: 12
categories:
- identity
- Web as Platform
---

XML Grrl [Eve Maler](http://www.xmlgrrl.com/) [pushed a string](http://www.xmlgrrl.com/blog) on [identity standards](http://www.xmlgrrl.com/blog/archives/2006/02/12/the-long-identity-tail/) for us long-tailers that piqued my interest in [OpenID.](http://openid.net/) ![openid-logo](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/openid-logo.jpeg)

Are you the kind of blogger who requires readers to authenticate to comment or do you allow open comments? Most blog comment interfaces allow the reader to submit a name, an email address, and a (blog) URL. Without authentication, anyone can submit any email address, and any URL.

There are a couple problems here. One of course is comment spam, but I'm not so focused on that since captcha's seem to do a pretty good job of heading that off -- at least for automated spam. The darker issue to me is misattribution -- the ability for anyone to claim that they are commenting _as the author_ of anyone else's blog.

If you follow John Udell's recent meme about the [professional blogosphere](http://weblog.infoworld.com/udell/2006/02/17.html#a1389) you gain an appreciation for the threat -- or at least the huge missed opportunity. John's idea is that over time, more professionals will push more of their professional output to the web and that an accessible record of their professional life will emerge. Viewed in this light, the sum total of a person's writings on the web become properly their "identity" -- or at least the online reflection of it.  In order for this to work though, the reader of the work must have an ability to find work by a particular person, and filter out misattributions -- both intentional and unintentional.

So just turn on password authentication for commenters and the problem will be solved right?  Um, well, hang on...  Now every blogger becomes a certification authority.  Every blogger has to hand out accounts on her blog and in doing so, certify that the identity information associated with those accounts is self-consistent.  Are folks going to bother to sign up for an account on your little backwater blog just to leave a comment, or are they going to move on.  Barriers to signing up include the [initial time delay](http://en.wikipedia.org/wiki/Don%27t_Make_Me_Think), and the need for the reader to remember the credentials and endure the subsequent time delay in the unlikely event that he wants to comment on your blog a second time.

Is there a way to validate a commenter's blog ownership claim without your having to go into the CA business.  Can this be achieved in a way that isn't off-putting to commenters? We're talking economics here folks.  And barriers to communication trade.

This is where [OpenID](http://openid.net/) comes in.  OpenID allows you to use your blog as an online anchor for your own identity and to authenticate commenters to your blog.  Install a [WordPress plugin](http://the-notebook.org/12/01/2006/openid-comments-for-wordpress/) ([others](http://www.lifewiki.net/openid/OpenIDStatus) are available) and you're off to the races.  If you use LiveJournal [the work](http://www.livejournal.com/openid/about.bml) is already done for you.

Here's how it works...


#### Your Blog Acting as Your Identity Server


Imagine you want to post a comment to SmallBlog.  If SmallBlog supports OpenID, you will be prompted for a URL along with your comment. The URL is your blog URL.  Technically it can be any URL that points to a page that has a link with rel="openid.server" pointing to your OpenID server -- which is just a reference to some PHP script hosted on your blog site (hint: it came with your OpenID plugin.)  You see how the framework is flexible, but in practice everything is just running on your blog site for the simple case.

So the comment post form on SmallBlog looks up your OpenID server and asks it to authenticate you and your OpenID server does that -- using a cookie from your browser.  Now this is the cool part.  The cookie is your blog software's cookie.  So if you're "logged in" to your own blog, then the cookie will be present, and will be passed to your OpenID server and you will be authenticated.  The OpenID server actually redirects the (requesting) browser to a page that lets you decide how long the session should last. ![openid-trust-choices1](/assets/{{page.path | remove_first: '_posts/' | remove: '.markdown'}}/openid-trust-choices1.gif)You make your selection and bang! you're back at SmallBlog and your comment has been submitted -- marked with your URL/identity.

If you are not logged in to your blog when you try to comment on SmallBlog then the cookie will not be present and your OpenID authentication will fail.  So that's the key.  You log in to your own blog and as long as you're logged in there, any comments you make on other folks' blogs from that browser, will succeed authentication.


#### Your Blog Acting as a Client of an OpenID Server


In the example just given, SmallBlog was acting as a client of your blog/identity server.  The plugins handle both sides so your blog can act as your identity server and it can also authenticate (foreign) commenters.


#### What it Doesn't Do -- Next Steps


OpenID does not provide a way for readers (of comments) to validate the identity of the commenter.  For now it only allows the blog itself to do that.  So evil blogs could lie about having validated their comments.  With OpenID as a foundation however, we can envision an explosion of layered protocols.

Imagine a [microformat](http://microformats.org/) that lets me link to my public key from my blog.  With OpenID as the foundation, we could build a protocol that would enable me to digitally sign my comments on SmallBlog.  Later, these could be validated by readers, using my publicly available key.  And that key is valid because it's linked to from the page referenced by the identifying URL.

The mind boggles at other uses for OpenID, but that will have to wait for another post. Now if you want to tell me I'm full of it, you'll have to authenticate with OpenID to do so. Ah, life in the bubble.
