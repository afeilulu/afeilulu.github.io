---
layout: post
title: Share lantern proxy
date: 2016-01-12 13:56
author: afeilulu
comments: true
categories: [program]
---
Lantern is great tools, it is still in development. But for now, it only supports on Mac, PC. Yes, no mobile version until now. So, let's share it with you mobile device.

After install and start lantern, modify configuration in .yaml file, which exists at

on MAC : ~/Library/Application\ Support/Lantern/lantern-2.0.10.yaml

on PC : %AppData%Lantern

Change addr option from "addr:127.0.0.1:8787" to "addr:0.0.0.0:8787".

Restart lantern, set your mobile proxy with "192.168.1.100:8787".

Enjoy!

&nbsp;
