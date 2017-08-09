---
layout: post
title: Android BootReceiver does not work
date: 2012-08-31 10:36
author: afeilulu
comments: true
categories: [Android, program]
---
First of all,the app should not be installed on your SD card,if you want it accept boot_complete action.Because android would mount SD card after boot completed.

Starting with 3.1 when applications are installed they are in a “stopped” state so they will not be able to run until the user explicitly launches them. Pressing Force Stop will return them to this state.

once the user runs the app for the first time (and does not Force Stop it), everything behaves as before — a reboot will cause BOOT_COMPLETED broadcasts to be received and so on. However, if the user installs the app, until and unless they run the app manually, no broadcasts will be received.h

This explaining does not for boot receiver only, but for all receiver also.
