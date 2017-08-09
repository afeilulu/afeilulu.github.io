---
layout: post
title: JNA UnsatisfiedLinkError, while jna.library.path is set
date: 2012-06-09 10:27
author: afeilulu
comments: true
categories: [java, program]
---
When I am running cling mediarenderer on win7(64), the following command is executed.
<blockquote>F:\&gt;java -Djna.library.path="C:\Program Files (x86)\OSSBuild\
GStreamer\v0.10.6\lib\gstreamer-0.10" -jar cling-mediarenderer-1.0.5-standalone.
jar -w</blockquote>
But I get JNA UnstatifiedLinkError.After digging, the reason should be as following : <a href="http://stackoverflow.com/questions/6823870/jna-unsatisfiedlinkerror-but-jna-library-path-is-set">referring here</a>

In my experience you usally see this error when calling 32bit native dlls from a 64bit jvm on 64bit Win7. Win7 works differently for 64bit and 32bit applications.

On 64bit Win7 you can call functions in native 32bit dll's, but you need to use a 32bit JVM.
<ol>
	<li>Download and install 32bit Java Runtime Environment (JRE). You can install a 32bit and 64bit jvm on the same machine, just make sure they are installed in seperate directories.</li>
	<li>Ensure you run the 32bit JRE instead of the default 64bit JVM when you execute your program. Either A) change your CLASSPATH and JAVA_HOME environment variables to point to the 32bit jvm, or write a script to set the CLASSPATH and JAVA_HOME and launch you application.</li>
</ol>
The reason for this is 32bit dll's are incompatible with 64bit applications, and 64bit Win7 uses a 32bit emulator to run 32bit applications. Although Windows Libraries might be called named xxxx32.dll (e.g. user32.dll) the libraries in the System32 folder on a 64bit Win7 are 64bit libraries, not 32bit, and the libraries in SysWOW64 are 32bit libraries, confusing right?!?

You can also place the library in the 32bit system folder (SysWOW64) and make the JNI call from within a 32bit JVM.

Check out the following link for a full explanantion as how 64bit Win7 handles libraries;

<a href="http://www.samlogic.net/articles/32-64-bit-windows-folder-x86-syswow64.htm" rel="nofollow">http://www.samlogic.net/articles/32-64-bit-windows-folder-x86-syswow64.htm</a>

And if you really want to help yourself get to sleep, trying starting on this ;)

<a href="http://msdn.microsoft.com/en-us/windows/hardware/gg463051.aspx" rel="nofollow">http://msdn.microsoft.com/en-us/windows/hardware/gg463051.aspx</a>
