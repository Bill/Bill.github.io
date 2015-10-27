---
author: bburcham
comments: true
date: 2008-10-15 01:47:15+00:00
layout: post
slug: are-ruby-session-ids-secure
title: Are Ruby Session ID’s Secure?
wordpress_id: 110
categories:
- Ruby
- Ruby on Rails
- security
---

The question of the security of cookie-based session storage in Rails has pretty much been settled it seems to me. [Out of the box](http://ryandaigle.com/articles/2007/2/21/what-s-new-in-edge-rails-cookie-based-sessions), Rails uses cookie-based session storage. When you generate a new Rails app you get a nice new 128 character long (numbers and lowercase letters) secret set in config.action_controller.session[:secret] in your Rails::Initializer. That secret is used to sign and validate cookies for your application. Now the cookie data isn't secret mind you, but it is tamper-proof. Good.

Now what if you don't use cookie-based session storage at all? Well, just because you aren't using cookie-based session storage doesn't mean you aren't using cookies. If your application has sessions at all, be they memcached ones or ActiveRecord ones, it is probably using cookies. It's using cookies to [store the session id](http://www.quarkruby.com/2007/10/21/sessions-and-cookies-in-ruby-on-rails#sinrails) so that when a request arrives, that id can be mapped to the corresponding session storage.

"So what" you say. "Well" I say… isn't it cool that Rails generates that big random secret for you when you use cookie-based session storage? When we are not using cookie-based session storage, and that secret is not generated, don't you wonder what secret is being used to secure your session id's? You see, a [session id must be hard to guess](http://www.technicalinfo.net/papers/WebBasedSessionManagement.html) lest bad people [gain access to your site](http://www.cgisecurity.com/lib/SessionIDs.pdf). Usually when you want to make something hard to guess, you start with a secret and mix that with something that changes a lot and hash the whole shebang. So I went in search of this other secret.

What I found was that Rails calls [CGI::Session#create_new_id](http://corelib.rubyonrails.org/classes/CGI/Session.html#M000366) to generate new session id's. That routine uses no secrete per se. It hashes (MD5) a combination of:



	
  1. the current date and time (expressed as a human-readable string)

	
  2. the microseconds elapsed since the last second (expressed as a human-readable string)

	
  3. a pseudo-random number greater than zero and less than one (from Kernel#rand)

	
  4. the current process id number

	
  5. the string 'foobar'


Notice there is no secret keying material there. "But what about the Kernel#rand call Bill!" I hear you saying. If you go have a look at [Kernel#rand](http://www.ruby-doc.org/core/classes/Kernel.html#M005977) and [Kernel#srand](http://www.ruby-doc.org/core/classes/Kernel.html#M005976) you'll see that if rand is called before srand is called with a number parameter then the random number will be generated from a combination of:



	
  1. the current time

	
  2. the process id number


So the security of these session ids hinges on the secrecy of current time (on the server running Ruby) and the process id. Given that the system time is returned in HTTP headers and process id's are often in the hundreds or thousands, it's only really the microseconds that are hard to guess here, from a statistical standpoint. [Others have expressed](http://www.nycbsdcon.org/2006/files/BSDRailsBenninger.pdf) similar concerns.

If you're worried about this two suggestions come to mind:



	
  * time out sessions on the server so that an attacker has to guess faster

	
  * monkey-patch [CGI::Session#create_new_id](http://corelib.rubyonrails.org/classes/CGI/Session.html#M000366) to hash its result with a great big old 128 character secret


**Updated**: October 15, 2008 expanded analysis of Kernel#rand and Kernel#srand and updated suggestions.
