---
layout: post
title: Customizing Toast
date: 2013-12-31 16:01
author: afeilulu
comments: true
categories: [Android, program]
---
<h1><strong>Show Time</strong></h1>
<span style="line-height:1.5em;">CountDownTimer class is the key.</span>

<code>// this will show toast for 10 seconds with interval of 1 second
timer = new CountDownTime(10000,1000)
{
public void onTick(long millisUntilFinished){
toast.show();
}
public void onFinish(){
toast.cancel();
}
}.start();</code>

Of course, you can cancel it at any time.
<code>timer.cancel();</code>
<h1><strong>Layout</strong></h1>
You can specify a view for toast ,either from a xml file or by initializing one in code.
<h2>custom_toast.xml</h2>
<code>&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:id="@+id/custom_toast_layout"
android:orientation="horizontal"
android:layout_width="fill_parent"
android:layout_height="fill_parent"
android:padding="8dp"
android:background="#DAAA"
&gt;
&lt;ImageView android:src="@drawable/myimage"
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:layout_marginRight="8dp"
/&gt;
&lt;TextView android:id="@+id/textToShow"
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:textColor="#FFF"
/&gt;
&lt;/LinearLayout&gt;</code>

<code>LayoutInflater inflater = getLayoutInflater();
View layout = inflater.inflate(R.layout.custom_toast,(ViewGroup)findViewById(R.id.custom_layout_toast));
TextView text = (TextView) layout.findViewById(R.id.textToShow);
text.setText("whatever you say");
Toast toast = new Toast(getApplicationContext());
toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
toast.setDuration(Toast.LENGTH_LONG);
toast.setView(layout);
toast.show();</code>
<h1>Position</h1>
As you seen above, setGravity could achieve where you want show toast view.

<code>toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);</code>
Three parameters: a Gravity constant, an x-position offset, and a y-position offset.
