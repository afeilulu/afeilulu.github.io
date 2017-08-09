---
layout: post
title: Xcode 7 with ios9 issues
date: 2015-12-11 23:04
author: afeilulu
comments: true
categories: [IOS, program, Xcode]
---
<h1>1.ios bitcode</h1>
Bitcode is an intermediate representation of a compiled program. Apps you upload to iTunes Connect that contain bitcode will be compiled and linked on the App Store. Including bitcode will allow Apple to re-optimize your app binary in the future without the need to submit a new version of your app to the store.

In Xcode7, bitcode is enabled in default.If you have any third party framework which don't not support bitcode, just update it to newest version. Or disable bitcode in your project.Remember if your project want to enable bitcode,all third party framework should support it also.
<ol>
<ol>
<ul>
	<li>ios : optional but default enabled</li>
	<li>watchos : must be enabled</li>
	<li>mac os : no bitcode</li>
</ul>
</ol>
</ol>
<h1>2.http cannot be used</h1>
In ios9, all http request is intercepted for security reason. And internet request is on TLS 1.2 SSL .If your server is not ready for https, change setting in info.plist like this:
<span class="sy0">&lt;</span>key<span class="sy0">&gt;</span>NSAppTransportSecurity<span class="sy0">&lt;/</span>key<span class="sy0">&gt;</span> <span class="sy0">&lt;</span>dict<span class="sy0">&gt;</span> <span class="sy0">&lt;</span>key<span class="sy0">&gt;</span>NSAllowsArbitraryLoads<span class="sy0">&lt;/</span>key<span class="sy0">&gt;</span> <span class="sy0">&lt;</span><span class="kw2">true</span><span class="sy0">/&gt;</span> <span class="sy0">&lt;/</span>dict<span class="sy0">&gt;</span>
<h1>3.Url Scheme should be in whitelist</h1>
Add following to info.plist.

&lt;key&gt;LSApplicationQueriesSchemes&lt;/key&gt;
&lt;array&gt;
&lt;string&gt;alipay&lt;/string&gt;
&lt;string&gt;wechat&lt;/string&gt;
&lt;string&gt;weixin&lt;/string&gt;
&lt;/array&gt;
<h1>4.library name change</h1>
.dylib become .tbd
