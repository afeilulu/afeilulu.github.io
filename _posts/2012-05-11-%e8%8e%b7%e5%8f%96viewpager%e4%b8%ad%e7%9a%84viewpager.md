---
layout: post
title: 获取viewpager中的fragment
date: 2012-05-11 09:11
author: afeilulu
comments: true
categories: [Android, program]
---
一般来讲，我们再使用viewpager的时候，是在代码中动态的添加fragment。此时，我们不能去指定fragment的id,也不能指定他的tag,如果你一定要去指定tag,运行时会提示，tag已经被指定。那么，在动态的添加了fragment之后，我们如何能获取到该fragment并修改其中的内容呢。两种方法：

第一种方法实际上是一种hack方法，并没有官方文档说明支持。代码如下：
<blockquote>SomeFragment someFragment  = getSupportFragmentManager().FindFragmentByTag("android:switcher:" + R.id.viewpager + ":0");

if (someFragment != null) //可能没有实例化

{

if (someFragment.getView() != null)

{

someFragment.updateView();//自定义方法更新

}

}</blockquote>
这里要记住的就是"android:switcher:" + R.id.viewpager + ":0" 这个字符窜表示的就是该fragment的tag，其中0 是fragment 在viewpager中的位置。

第二种方法更正规，因为第一种方法对基于FragmentPagerAdapter的实现是有效的，但对于FragmentStatePagerAdapter就无效了。因为FragmentStatePagerAdapter不会对传给FragmentManager的Fragment设置tag.
<blockquote>ViewPager pager = (ViewPager)findViewById(R.id.viewpager);

FragmentStatePagerAdapter f = pager.getAdapter();

SomeFragment someFragment = (SomeFragment)f.instantiateItem(pager,position);</blockquote>
instantiateItem(pager,position)方法会返回在position位置的fragment的引用。如果该fragment 已经实例化了,再次调用instantiateItem(pager,position)的时候，该方法并不会调用getItem()来再次实例化fragment，而是直接返回引用。
