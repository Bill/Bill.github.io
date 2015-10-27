---
author: bburcham
comments: true
date: 2006-11-07 12:14:34+00:00
layout: post
slug: choosing-a-dynamic-dns-service
title: Choosing a Dynamic DNS Service
wordpress_id: 36
categories:
- DNS
- dynamic
---

I recently had a look at a few dynamic DNS services.  They each provide simple HTTP interfaces for updating the the "A" record for a host (that's the one that maps a [Fully Qualified Domain Name](http://en.wikipedia.org/wiki/FQDN) to an [IP address](http://en.wikipedia.org/wiki/Ip_address)).  In each case you must provide credentials (username and password) as URL query parameters in the HTTP request.  If you want to protect those credentials then you'll want to use SSL by specifying the [https](http://en.wikipedia.org/wiki/Https) URI scheme.  With most of the services I looked at I found that https wasn't really usable because the certificates presented by those services were not valid.  In each such case, the provider was using a certificate whose subject (name) failed to match the FQDN of the actual host.  Only one provider presented a working dynamic DNS service with a valid certificate.  Without further ado:


### Service Didn't Work At All (http scheme)


[RegisterFly](http://registerfly.com/)


### Service Did Not Present Valid Certificate (https scheme)


[RegisterFly](http://registerfly.com/)

[zoneedit](http://zoneedit.com/)


### Service Worked and Presented Valid Certificate


[DNS Made Easy](http://www.dnsmadeeasy.com/)

So the big winner here is [DNS Made Easy](http://www.dnsmadeeasy.com/).  It was the only service that offered a functioning, secure dynamic DNS service.
