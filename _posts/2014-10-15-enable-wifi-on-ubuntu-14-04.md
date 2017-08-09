---
layout: post
title: Enable WIFI on Ubuntu 14.04
date: 2014-10-15 12:22
author: afeilulu
comments: true
categories: [program]
---
I have my laptop installed Ubuntu 14.04. Everything is fine except no wifi driver installed.

So, I need install driver manually.

* checking wifi hardware infomation
<pre>#lspci
03:00.0 Network controller: Broadcom Corporation BCM43228 802.11a/b/g/n
</pre>
* install drivers
<pre>#sudo apt-get install linux-headers-generic
#sudo apt-get install --reinstall bcmwl-kernel-source
</pre>

No need to reboot, you will get wifi enabled.
