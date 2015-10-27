---
author: bburcham
comments: true
date: 2009-08-06 17:08:21+00:00
layout: post
slug: upgrade-os-x-10-5-8-to-jdk-1-6
title: Upgrade OS X 10.5.8 to JDK 1.6
wordpress_id: 268
categories:
- Apple
- tool
tags:
- java
- jdk
- OS X
---

I didn't find any clear instructions on upgrading the JDK on OS X so I thought I'd share my findings.

First off, apparently Software Update does not automatically update the JDK. You have to manually download and run the updates. There are four updates to "Java for Mac OS X 1.5". Since I couldn't tell whether they were cumulative or not, I just downloaded and installed each one in sequence:


[update 1](http://www.apple.com/downloads/macosx/apple/application_updates/javaformacosx105update1.html)




[update 2](http://www.apple.com/downloads/macosx/apple/application_updates/javaformacosx105update2.html)




[update 3](http://www.apple.com/downloads/macosx/apple/application_updates/javaformacosx105update3.html)




[update 4](http://www.apple.com/downloads/macosx/apple/application_updates/javaformacosx105update4.html)



Now you'd think you'd be done, but those updates only install JDK 1.6—they don't make it the default. The way I did that was to change a link in the Framework:




    
    <span style="font-family:monospace, Monaco, 'Courier New', Courier, monospace;">cd /System/Library/Frameworks/JavaVM.framework/Versions
    sudo rm -fr CurrentJDK
    sudo ln -s 1.6 CurrentJDK</span>





Now I see:


` `

    
    <code>$ java -version
    java version "1.6.0_13"
    Java(TM) SE Runtime Environment (build 1.6.0_13-b03-211)
    Java HotSpot(TM) 64-Bit Server VM (build 11.3-b02-83, mixed mode)</code>







Now to see if it really works…







**Update 1 **8/7/09: That link I created wasn't quite right in the original post. It was creating a link with an absolute path. This apparently caused the Java Preferences app to fail to start and also caused WebKit/Safari to crash on all but the simplest web pages. Wierd. Corrected link creation above.
