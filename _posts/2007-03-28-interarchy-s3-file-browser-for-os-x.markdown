---
author: Bill Burcham
comments: true
date: 2007-03-28 00:39:02+00:00
layout: post
slug: interarchy-s3-file-browser-for-os-x
title: Interarchy S3 File Browser for OS X
wordpress_id: 64
categories:
- OS X
- tool
---

I use Amazon S3 to store backups from EC2. For a while I've been thinking I ought to back up some of the S3 data once in a while but I've been putting it off. One reason I've been procrastinating is because the S3 "file" management tool I've been using, [S3 Browser](http://people.no-distance.net/ol/software/s3/), doesn't support simple things like deleting or downloading folders. You may delete a folder (="bucket") only if it's empty. You may copy the files (="objects") in a folder but there is no convenient way to just copy the folder and all its contents at once.  
  
When I saw the [S3Fox](https://addons.mozilla.org/en-US/firefox/addon/3247) Firefox extension a while back I was encouraged. Unfortunately, S3Fox has some critical bugs on the Mac. In particular, folder downloads don't work -- apparently because S3Fox is using backslashes in destination paths a la Windows. You end up with empty folders on your Mac.  
  
[![Interarchy](http://nolobe.com/img/interarchyicon.png)](http://nolobe.com/interarchy/)I was rescued from a serious bout of the crankies by Nolobe's [Interarchy](http://nolobe.com/interarchy/). This Mac-only file transfer application apparently supports lots of protocol standards and lots of interesting automation features blah-blah-blah but what's important to me is -- it has S3 support! Woo hoo! So I downloaded the free 14 day trial and used it to browse my S3 buckets. In a nonce I had downloaded a couple hundred megabytes of precious machine images and subversion snapshots. Now they sit on my disk, ready for their final DVD resting place. I can sleep tonight.  
  
Oh, and Interarchy is a lickable OS X app too. I have no idea yet if I'll prefer it to [Transmit](http://www.panic.com/transmit/) (tall order) but for S3 work from the Mac it looks like a must-have.
