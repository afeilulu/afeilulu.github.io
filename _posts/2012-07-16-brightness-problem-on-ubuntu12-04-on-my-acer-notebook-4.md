---
layout: post
title: Brightness problem on Ubuntu12.04 on my acer notebook
date: 2012-07-16 19:59
author: afeilulu
comments: true
categories: [program]
---
The brightness cannot be controlled after your installation of Ubuntu12.04.

Do as following,it works:
<ol>
	<li>open a terminal</li>
	<li>edit "/etc/default/grub" (gedit/nano/vi/vim as you like)</li>
	<li>change the line :GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" to :GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi_backlight=vendor"</li>
	<li>sudo update-grub</li>
	<li>save and reboot.</li>
	<li>good luck.</li>
</ol>
