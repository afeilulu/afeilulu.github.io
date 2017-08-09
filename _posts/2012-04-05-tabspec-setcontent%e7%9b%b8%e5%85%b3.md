---
layout: post
title: TabSpec.setContent相关
date: 2012-04-05 16:14
author: afeilulu
comments: true
categories: [Android, program]
---
首先要明白
<ul>
	<li>Activity.setContentView 以Layout的id 作为参数(R.layout.*)</li>
	<li>TabSpec.setContent 以View的id作为参数(R.id.*)</li>
</ul>
如果tab contect定义文件是以include的形式包含在fragment的layout定义文件中，如:
<blockquote>&lt;TabWidget android:id="@android:id/tabs"
android:layout_width="fill_parent"
android:layout_height="wrap_content"
android:background="@drawable/tab_background" /&gt;

&lt;FrameLayout android:id="@android:id/tabcontent"
android:layout_width="0dp"
android:layout_height="0dp"
android:layout_weight="0" /&gt;
&lt;include android:id="@+id/tab_detail_1"
layout="@layout/tab_detail_1" /&gt;
&lt;include android:id="@+id/tab_detail_2" layout="@layout/tab_detail_2" /&gt;
&lt;include android:id="@+id/tab_detail_3" layout="@layout/tab_detail_3" /&gt;</blockquote>
在动态装载tab内容时，需要采取下面的方法
<blockquote>View wv = this.getActivity().getLayoutInflater().inflate(R.layout.tab_detail_1, mTabHost.getTabContentView(), true);
mTabHost.addTab(mTabHost.newTabSpec(TAG_TAB_1)
.setIndicator(buildIndicator(R.string.tab_detail_1))
.setContent(wv.getId()));</blockquote>
而不能直接使用
<blockquote>mTabHost.getTabContentView(), true);
mTabHost.addTab(mTabHost.newTabSpec(TAG_TAB_1)
.setIndicator(buildIndicator(R.string.tab_detail_1))
.setContent(R.id.tab_detail_1))</blockquote>
这是因为，虽然有R.id.tab_detail_1定义，但是该layout在fragment的onCreateView方法中并没有填充该组件，需要手动装载后，再将该组件View的id 给setContent 使用。
