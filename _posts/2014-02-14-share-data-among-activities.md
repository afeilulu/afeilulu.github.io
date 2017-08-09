---
layout: post
title: Share data among activities
date: 2014-02-14 10:06
author: afeilulu
comments: true
categories: [program]
---
In android development,it is unavoidable to share data among activities. I will introduce some common ways to achieve and give some advice while using them in a short word.
<ul>
	<li>
<h2>Sending data inside intent</h2>
</li>
</ul>
An official way.It is an one-way approach and you'd better carry data as less as you can in one intent.Primitive data or string is the best.Too much heavy data may slow down your application heavily especially implement Serializable interface. So stay away from it. Parcelable interface would be better while its implement is not as easy as you think.
<ul>
	<li>
<h2>A singleton class</h2>
</li>
</ul>
A singleton pattern can make you get what you want at any time as long as in one process.
<pre><code>
public class DataHolder {
  private String data;
  public String getData() {return data;}
  public void setData(String data) {this.data = data;}
  private static final DataHolder holder = new DataHolder();
  public static DataHolder getInstance() {return holder;}
} </code></pre>
while using
<pre><code>String data = DataHolder.getInstance().getData();</code></pre>
<ul>
	<li>
<h2>Application Singleton</h2>
</li>
</ul>
An extending and an instance of android.app.Application class.It is created while application is launched.You need specify the class name in application node inside Android_manifest file like this.
<pre><code>appname="CustomizeName"</code></pre>
Extending the application class
<pre><code>
 import android.app.Application;
 public class CustomizeName extends Application {
   private String data;
   public String getData() {return data;}
   public void setData(String data) {this.data = data;}
 }
 </code></pre>
After the main activity is lauched,you could get or set data at any activity in this application.
<pre><code>
 CustomizeName app = (CustomizeName) getApplicationContext();
 app.setData(data);
 String data = app.getData();
 </code></pre>
<ul>
	<li>
<h2>Static fields</span></h2>
</li>
</ul>
Same as singleton class, beside you could can access data directly.
<pre><code>public class DataHolder {
  private static String data;
  public static String getData() {return data;}
  public static String setData(String data) {this.data = data;}
}

String data = DataHolder.getData();

</code></pre>
<ul>
	<li>
<h2>HashMap of WeakReference</h2>
</li>
</ul>
Same as singleton,but allowing the garbage collector to remove unreferenced objects.
<pre><code>
public class DataHolder { 
  Map&lt;String, WeakReference&lt;Object&gt;&gt; data = new HashMap&lt;String, WeakReference&lt;Object&gt;&gt;(); 
  void save(String id, Object object) { 
    data.put(id, new WeakReference(object)); 
  } 
  Object retrieve(String id) { 
    WeakReference objectWeakReference = data.get(id); 
    return objectWeakReference.get(); 
  } 
} 

DataHolder.getInstance().save(someId, someObject); 
DataHolder.getInstance().retrieve(someId);</code></pre>
<ul>
	<li>
<h2>Persist objects to disk</h2>
</li>
</ul>
Some of the ways to persist objects include:
<ol>
	<li>Save into the shared preferences</li>
	<li>Save int a sqlite database</li>
	<li>Save into a file.Please avoid this.</li>
</ol>
