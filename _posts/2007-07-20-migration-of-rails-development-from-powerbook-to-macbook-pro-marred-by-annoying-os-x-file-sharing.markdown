---
author: bburcham
comments: true
date: 2007-07-20 16:34:58+00:00
layout: post
slug: migration-of-rails-development-from-powerbook-to-macbook-pro-marred-by-annoying-os-x-file-sharing
title: Migration of Rails Development from PowerBook to MacBook Pro Marred by Annoying
  OS X File Sharing
wordpress_id: 81
categories:
- OS X
- Ruby on Rails
---

Well I spent yesterday getting the new computer in shape and it'll now run all my Rails projects. Overall the process has been surprisingly easy. The initial 45 minute FireWire brain transfer was extremely effective. After that, most productivity apps Just Workedâ„¢. Loves me some Universal Binaries.

For the Rails folks out there I thought you'd be interested to know that this time around I chose to use Locomotive for my Rails stack and it has mostly worked very well. Last time I configured a brand new development machine (late '05) I went through the heck of installing a slew of ports for all the pieces (ruby, gem, gems, app server, web server, etc.) This time around I eschewed the 14 pages of instructions in favor of the single download and got myself some needed RMagick support in the bargain. I had successfully put off installing RMagick for about 18 months (thanks Captchator!) but recently started a project that just had to have RMagick. If you wonder why I've been avoiding RMagick then read this.

In moving my development environment from the old machine to the new, mysql didn't automatically come over the firewire so I had to download and install it on the new machine. I needed to dump the database and move it to the new machine and load it. Turns out that moving that file over was about the most annoying part of this whole process.

So I created the dump on the old machine:

`mysqldump -u root -pyour-password-here --single-transaction --all-databases > mysql_backup.sql`

And then went into System Preferences/Sharing and turned on Personal File Sharing. On the new machine I saw the shared directory, but the newly created file wasn't there. All the other files were there. I've seen this before and presume it must be some sort of caching or timing issue. Old files show up, new files don't. And of course there is no way to "refresh" a finder view.

So I switched gears and tried "FTP Access" in Sharing. Neither Transmit nor Interarchy were able to connect via FTP to the old machine. Finally, a friend recommended SFTP (SSH SFTP). And um, after clicking on each option in Sharing I saw that "Remote Login" mentions SSH so I thought I'd run that and sure enough I was able to use SFTP via Transmit to get the file. Whew.

Once the database dump was moved over I simply loaded it on the new database server. Remember a newly created mysql database has no passwords.

`mysql < mysql_backup.sql`

You'll notice that logins you brought over don't work until you restart mysqld. Once you do that, you're database has been replicated and your various logins and security restrictions will be in effect.

One other odd thing (besides the file sharing annoyance) was that while Locomotive purports to be a Universal Binary application it doesn't actually work properly after the firewire transfer. It seemed like it was working -- the application itself ran and it would actually start certain Rails apps. Problems had to do with RMagick not working (there's a recurring theme here :) My solution was to reinstall Locomotive and the RMagick bundle afresh. After that everything worked great.

To summarize, here's the main things I did to move my Rails development from PowerBook to MacBook Pro:

   1. run the automatic firewire system transfer to get most of your environment moved over
   2. install Apple Software Updates and reboot (you should do this early on with any new machine)
   3. install mySQL 5.0
   4. dump mySQL database on old machine, transfer file using "remote login" and SFTP, load database on new machine and reboot mySQL
   5. install X Code 2.4.1
   6. re-install Locomotive (even though it transferred via Firewire):
         1. move Applications/Locomotive2 to trash
         2. download/install Locomotive
   7. install Locomotive RMagick bundle if you need it
   8. finally, under the Locomotive Terminal I had to install a few gems for my projects: ruby-debug, chronic

That's pretty much it. All my Rails projects are up and running on the new machine. A little faster and with 60px less vertical space to work with. Not nearly as bad as it might have otherwise been I suppose.

Bear in mind these are not complete instructions for getting from a clean MacBook Pro to a Rails development environment. This is just about migrating from the PowerPC to Intel with a minimum of pain. It's been pretty painless so far. Time will tell how well the Locomotive choice holds up. I'll keep you updated.
