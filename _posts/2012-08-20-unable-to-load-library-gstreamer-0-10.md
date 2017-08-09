---
layout: post
title: Unable to load library 'gstreamer-0.10'
date: 2012-08-20 18:55
author: afeilulu
comments: true
categories: [program, ubuntu]
---
When try to run any GStreamer-Java application on Ubuntu,some error happened as following
UnsatisfiedLinkError: Unable to load library 'gstreamer-0.10': libgstreamer-0.10.so: cannot open shared object file: No such file or directory
What you need is to install some packages
<pre>sudo apt-get install libgstreamer-plugins-base0.10-dev libgstreamer0.10-dev libglib2.0-dev</pre>
