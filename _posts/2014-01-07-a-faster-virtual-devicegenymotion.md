---
layout: post
title: A faster virtual device:Genymotion
date: 2014-01-07 15:21
author: afeilulu
comments: true
categories: [program]
---
Genymotion is not an emulator of Android but a virtualization based on virtual-box.This solution is the key of performance for android virtual device.Speed is an enormous advantage for developers.You can find how install it at <a href="http://www.genymotion.com/features/">here</a>.

Some Tips:
<ol>
	<li>Ensure you have virtual-box installed firstly.</li>
	<li>If you encounted "INSTALL_FAILED_CPU_ABI_INCOMPATIBLE" error, install <a href="http://goo.gl/JBQmPa">ARM Translation Installer v1.1</a> by dragging zip file onto virtual device HomeScreen.<span style="text-decoration:underline;">
</span></li>
	<li>Install GApps as you need in the same way.
<ul>
	<li><a href="http://goo.im/gapps/gapps-jb-20130813-signed.zip">4.3</a></li>
	<li><a href="http://goo.im/gapps/gapps-jb-20130812-signed.zip">4.2</a></li>
	<li><a href="http://goo.im/gapps/gapps-jb-20121011-signed.zip">4.1</a></li>
</ul>
</li>
	<li>Sometimes you may encount  libGL.so file not found error.Resolving like below:
<ul>
	<li>find it by command if you are on linux "locate libGL.so | xargs ls -al", and check whether the file exists or not.</li>
</ul>
</li>
</ol>
This is output on my Ubuntu:
<code>
chen@Aspire:~$ locate libGL.so|xargs ls -al
-rw-r--r-- 1 chen chen 440282 Feb 14 2011 /home/chen/.local/share/Steam/ubuntu12_32/steam-runtime/amd64/usr/share/doc/nvidia-cg-toolkit/examples/Tools/trace/libGL.so.1.gz
-rw-r--r-- 1 chen chen 353420 Feb 11 2011 /home/chen/.local/share/Steam/ubuntu12_32/steam-runtime/i386/usr/share/doc/nvidia-cg-toolkit/examples/Tools/trace/libGL.so.1.gz
-rw-r--r-- 1 chen chen 440282 Feb 14 2011 /home/chen/.local/share/Steam/ubuntu12_32/steam-runtime.old/amd64/usr/share/doc/nvidia-cg-toolkit/examples/Tools/trace/libGL.so.1.gz
-rw-r--r-- 1 chen chen 353420 Feb 11 2011 /home/chen/.local/share/Steam/ubuntu12_32/steam-runtime.old/i386/usr/share/doc/nvidia-cg-toolkit/examples/Tools/trace/libGL.so.1.gz
lrwxrwxrwx 1 root root 14 Oct 12 2012 /usr/lib/i386-linux-gnu/mesa/libGL.so.1 -&gt; libGL.so.1.2.0
-rw-r--r-- 1 root root 383000 Oct 12 2012 /usr/lib/i386-linux-gnu/mesa/libGL.so.1.2.0
-rw-r--r-- 1 root root 276320 Dec 5 02:23 /usr/lib/i386-linux-gnu/primus/libGL.so.1
lrwxrwxrwx 1 root root 39 Apr 24 2013 /usr/lib/virtualgl/fakelib/64/libGL.so -&gt; ../../../x86_64-linux-gnu/librrfaker.so
lrwxrwxrwx 1 root root 34 Apr 24 2013 /usr/lib/virtualgl/fakelib/libGL.so -&gt; ../../i386-linux-gnu/librrfaker.so
lrwxrwxrwx 1 root root 14 Oct 12 2012 /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1 -&gt; libGL.so.1.2.0
-rw-r--r-- 1 root root 414896 Oct 12 2012 /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0
-rw-r--r-- 1 root root 269264 Dec 5 02:22 /usr/lib/x86_64-linux-gnu/primus/libGL.so.1
</code>

And you will find "/usr/lib/i386-linux-gnu/mesa/libGL.so.1.2.0" and "/usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0" is what you need. Considering I am using a 64bit system, link "/usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0" to "/usr/lib/libGL.so" is all you need.
<code>
sudo ln -s /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0 /usr/lib/libGL.so
</code>

Restart your genymotion virtual device , the "Failed to load libGL.so" error now gone. :)

Now Happy coding with Android Studio or Eclipse.
