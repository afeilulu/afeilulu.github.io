---
layout: post
title: Adb over network
date: 2012-09-20 19:12
author: afeilulu
comments: true
categories: [Android, program]
---
While in andoid development,Sometimes you have not the required usb driver of some devices. But you want to debug it wi th your development, how could we achieve this ?

Adb provide another way through network.Connecting devices by command:
<code>adb connect your-device-ip-address(default port is 5555)</code>

And disconnect by:
<code>adb connect your-device-ip-address</code>

After success, you can debug your software as connected by usb port.

There is another thing to attention.You need check on "Allow remote debugging" or "Allow adb over network" in android system setting. If the options are not shown in setting , we also get a command to achieve this.

Enable

<code>
setprop service.adb.tcp.port 5555
stop adbd
start adbd
</code>

Disable

<code>
setprop service.adb.top.port -1
stop adbd
start adbd
</code>

Of cause, you need root to run these command.
