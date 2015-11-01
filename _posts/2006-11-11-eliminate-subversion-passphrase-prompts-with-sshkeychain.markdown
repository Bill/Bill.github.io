---
author: Bill Burcham
comments: true
date: 2006-11-11 21:44:35+00:00
layout: post
slug: eliminate-subversion-passphrase-prompts-with-sshkeychain
title: Eliminate Subversion Passphrase Prompts with SSHKeychain
wordpress_id: 40
categories:
- OS X
- tool
---

One of the things I really like about OS X is the [Keychain Access](http://en.wikipedia.org/wiki/Keychain_Access) application.  Most Mac applications (but [not Firefox](http://wiki.mozilla.org/Firefox:3.0_Password_Manager#Mac_OS_X_Integration)) that deal with passwords or certificates enlist Keychain Access services.  The upshot is that you don't need to manage a password file yourself, and you don't need a third-party secure credential management application.

Unless you use [ssh](http://en.wikipedia.org/wiki/Secure_Shell) on the command line a lot.

Unfortunately,  on OS X, the ssh command line tools are not integrated with Keychain Access.  This is unfortunate for a couple reasons.  First, it means that you have the choice (with the ssh command line tools) to either passphrase protect each key or not.  If you decide not to then you expose that key to attack (if someone gets access to your disk).

Now if you do decide to passphrase-protect an ssh key a dilemma arises.   You will be prompted for that passphrase each time the key is required for an ssh operation.  Every time you ssh to a remote machine, or run scp or sftp, you'll be prompted.  And if you use [Subversion](http://www.google.com/url?sa=t&ct=res&cd=1&url=http%3A%2F%2Fsubversion.tigris.org%2F&ei=mUBWRdECpNaDA9XS4K4J&usg=__WUg-CQQMbA6iC8pYlsmw6tsjoXE=&sig2=LNgmSc99NF6dsb76Pk8BkA) over ssh you'll be prompted (for a passphrase) for each of your remote subversion commands.  Ugh.

Until Apple adds direct integration between the ssh command line tools and Keychain Access we have to make due with one of the "ssh agents".  There is a command-line one that ships with OS-X called ssh-agent if you like that sort of thing.  I was hoping for a mouse-driven one and went on a little expedition.

The first one I looked at, [SSH Agent](http://www.phil.uu.nl/~xges/ssh/), is a nice enough Cocoa app but it's got a fatal flaw.  The whole point of this thing is to  enable me to avoid entering passphrases.  To do that, the app needs to access a passphrase stored in Keychain Access.  Well SSH Agent looks like it _intends_ to support that -- but unfortunately, the "add to keychain" control is disabled.  As a result you have to go to Keychain Access and manually get your passphrase and then paste it into SSH Agent each time you you want to activate an ssh key.

The second Cocoa app I looked at, [SSHKeychain](http://www.sshkeychain.org/) fared better in this regard.  It integrates nicely with Keychain Access.  The interface is a bit quirky but if you follow the [directions](http://trac.sshkeychain.org/cgi-bin/trac.cgi/wiki/INSTALL) you should be up in no time.  Note that with each of the ssh agents I tested, there were situations where I had to log out (of OS X) and back in to get the ssh command line tools to notice the agent.

Any chance of integrated SSH key management making it into Keychain Access in time for [Leopard](http://www.apple.com/macosx/leopard/index.html)?
