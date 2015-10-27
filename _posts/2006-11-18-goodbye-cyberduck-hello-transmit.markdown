---
author: bburcham
comments: true
date: 2006-11-18 12:58:08+00:00
layout: post
slug: goodbye-cyberduck-hello-transmit
title: Goodbye Cyberduck, Hello Transmit
wordpress_id: 43
categories:
- OS X
- tool
---

I recently switched from Fugu to Cyberduck.  At the time I [considered](http://www.memerocket.com/2006/11/11/goodbye-fugu-hello-cyberduck/) moving to Panic's [Transmit](http://www.panic.com/transmit/) but didn't see how to get SSH certificate-based authentication to work.

Well, a nice person at Transmit support answered my question.  Interestingly, while perhaps a little non-obvious to the SSH-non-initiate, the solution is actually elegant -- do nothing.  That's right, Transmit works directly with the ssh command-line infrastructure.  This means if you've got your identities (keys and certificates) stored in the ~/.ssh directory you're good to go.  Further, if you're using an SSH Agent (like [SSHKeychain](http://www.memerocket.com/2006/11/11/eliminate-subversion-passphrase-prompts-with-sshkeychain/)) you'll never even be prompted for a passphrase by Transmit.
While I like Cyberduck a lot, the edge goes to Transmit -- even at a cost of $29.95.  Here goes:



	
  1. Transmit is **snappier**.  I haven't measured file transfer speeds, but directory listing and navigation is definitely snappier than Fugu or Cyberduck.  I spend a lot of time navigating around remote filesystems and I don't move a lot of great big files so reponse time for listings is crucial.

	
  2. Transmit can be configured to edit a file on **double click**.  In Cyberduck you have to select the file and hit command-j to edit it -- annoying.  Of course in any case, the file must be downloaded -- but it's downloaded to some mystery location where you don't have to worry about cleaning it up.

	
  3. Transmit supports **column view** (browser view) just like Finder.  Cyberduck does offer a contextual view but it's more of a Windows-style-double-click-a-folder-to-expand-subfolders affair.  Not that there's anything wrong with that.


There are couple shortcomings worth mentioning too:

	
  1. While Transmit offers tab support -- innovative among the file transfer clients I've looked at -- the workflow is a bit **clumsy**.  If you have a configuration saved as a favorite there is no way to quickly and directly open it in a new tab.  Instead you must go through four steps.  How about an "open favorite in new tab" option in Preferences?  Currently you must:

	
    1. open a new tab (command-t or via File menu)

	
    2. select the "favorites" tab within that new tab

	
    3. select the "Favorites" folder

	
    4. double-click the favorite you want


On the upside -- once you've done this once, Transmit remembers that you want the Location tab and the Favorites folder so the middle two steps drop out.  Still, one step would be better than two steps.



	
  2. When configuring a new connection for password authentication, Transmit won't allow **paste** into password field -- you have to type the password from the keyboard.  I keep all my credentials in Keychain Access so it's real easy to get 'em on the clipboard.  Minor one-time annoyance.



All in all I'm enjoying Transmit.  It's been reliable and comfortable.  I'm off to pay my $29.95 now...
