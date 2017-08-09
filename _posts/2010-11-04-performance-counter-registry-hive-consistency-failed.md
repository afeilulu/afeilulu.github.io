---
layout: post
title: performance counter registry hive consistency failed
date: 2010-11-04 11:38
author: afeilulu
comments: true
categories: [program]
---
今天在安装sql server 2008 R2过程中检测到错误 "<span style="font-family:Consolas, Monaco, 'Courier New', Courier, monospace;line-height:18px;font-size:12px;white-space:pre;">performance counter registry hive consistency failed</span>"

最彻底的解决办法 ：<a href="http://support.microsoft.com/kb/300956">http://support.microsoft.com/kb/300956</a>

最迅速的解放办法 :

在cmd中输入lodctr /R:PerfStringBackup.ini 之后点击Re_run.即解决即继续～
