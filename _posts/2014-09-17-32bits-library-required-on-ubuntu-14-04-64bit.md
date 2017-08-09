---
layout: post
title: 32bits library required on Ubuntu 14.04 64bit
date: 2014-09-17 16:43
author: afeilulu
comments: true
categories: [Android, program, ubuntu]
---
Android development sdk requires 32bits library runtime. Remember that.

When you develop on Ubuntu 64bit,especailly on version after 13.10, it would happen encounting some error like
<pre>java.io.IOException: Cannot run program "/opt/android-studio/sdk/build-tools/android- 4.2.2/aapt": error=2, No such file or directory</pre>
or some others while compliing.

What you need to do is installation them though. Before 13.10 you could install like this:
<pre>sudo apt-get install ia32-libs</pre>
But after 13.10, you can not find ia32-libs on unversial repository.Install the follow packages would resolve.
<pre>sudo apt-get install lib32stdc++6
sudo apt-get install lib32z1</pre>
you could know more at <a href="http://askubuntu.com/questions/147400/problems-with-eclipse-and-android-sdk">here</a>
