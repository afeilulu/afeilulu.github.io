---
layout: post
title: Dialog style 
date: 2012-10-23 19:01
author: afeilulu
comments: true
categories: [Android, Aside, program]
---
&nbsp;
<blockquote>&lt;style name="dialog" parent="@android:style/Theme.Dialog"&gt; &lt;!-- 在系统Dialog样式基础上，相当于继承系统样式 --&gt;
&lt;item name="android:windowFrame"&gt;@null&lt;/item&gt; &lt;!-- Dialog的windowFrame框为无 --&gt;
&lt;item name="android:windowIsFloating"&gt;true&lt;/item&gt; &lt;!-- 是否浮现在activity之上 --&gt;
&lt;item name="android:windowIsTranslucent"&gt;false&lt;/item&gt; &lt;!-- 是否半透明 --&gt;
&lt;item name="android:windowNoTitle"&gt;true&lt;/item&gt; &lt;!-- 是否显示title --&gt;
&lt;!-- &lt;item name="android:windowBackground"&gt;@drawable/dia_bg&lt;/item&gt; --&gt; &lt;!-- 设置dialog的背景 --&gt;
&lt;item name="android:backgroundDimEnabled"&gt;false&lt;/item&gt; &lt;!-- 背景是否模糊显示 --&gt;
&lt;/style&gt;</blockquote>
&nbsp;
