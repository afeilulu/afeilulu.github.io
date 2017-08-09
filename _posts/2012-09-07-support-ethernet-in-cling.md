---
layout: post
title: Support ethernet in cling.
date: 2012-09-07 09:48
author: afeilulu
comments: true
categories: [Android, program, upnp]
---
<a href="http://4thline.org/projects/cling">Cling</a> is an upnp stack for dlna development especially for mobile, it support android default. But when you want to use it for some STB device that have ethernet with itself. Cling could not help you.

But there is an unoffical way to enable ethernet.To do a hack on original source, you need a patch.

Apply the patch and build the maven project to get the new cling-core-2.0-SNAPSHOT.jar file.
Remembering copy it with the there seamless*.jar file to your application library directory.

That's done.

Some useful link:
<a href="http://4thline.org/projects/mailinglists.html#nabble-td3301540">http://4thline.org/projects/mailinglists.html#nabble-td3301540</a>
<a href="http://4thline.org/projects/mailinglists.html#nabble-td3999287|a4000748">http://4thline.org/projects/mailinglists.html#nabble-td3999287|a4000748</a>


Here is the patch file:
<code>
diff --git a/core/src/main/java/org/fourthline/cling/android/AndroidNetworkAddressFactory.java b/core/src/main/java/org/fourthline/cling/android/AndroidNetworkAddressFactory.java
index 8116d79..d16abad 100644
--- a/core/src/main/java/org/fourthline/cling/android/AndroidNetworkAddressFactory.java
+++ b/core/src/main/java/org/fourthline/cling/android/AndroidNetworkAddressFactory.java
@@ -17,12 +17,6 @@
 
 package org.fourthline.cling.android;
 
-import android.net.wifi.WifiManager;
-import org.fourthline.cling.model.Constants;
-import org.fourthline.cling.model.ModelUtil;
-import org.fourthline.cling.transport.spi.InitializationException;
-import org.fourthline.cling.transport.spi.NetworkAddressFactory;
-
 import java.net.Inet4Address;
 import java.net.Inet6Address;
 import java.net.InetAddress;
@@ -35,6 +29,13 @@ import java.util.Enumeration;
 import java.util.List;
 import java.util.logging.Logger;
 
+import org.fourthline.cling.model.Constants;
+import org.fourthline.cling.model.ModelUtil;
+import org.fourthline.cling.transport.spi.InitializationException;
+import org.fourthline.cling.transport.spi.NetworkAddressFactory;
+
+import android.net.wifi.WifiManager;
+
 /**
  * Implementation appropriate for Android environment, avoids unavailable methods.
  * <p>
@@ -56,7 +57,7 @@ public class AndroidNetworkAddressFactory implements NetworkAddressFactory {
     /**
      * Defaults to an ephemeral port.
      */
-    public AndroidNetworkAddressFactory(WifiManager wifiManager) throws InitializationException {
+    public AndroidNetworkAddressFactory(Object wifiManager) throws InitializationException {
 
         wifiInterface = getWifiNetworkInterface(wifiManager);
 
@@ -147,14 +148,14 @@ public class AndroidNetworkAddressFactory implements NetworkAddressFactory {
 
     // Code from: http://www.gubatron.com/blog/2010/09/19/android-programming-how-to-obtain-the-wifis-corresponding-networkinterface/
 
-    public static NetworkInterface getWifiNetworkInterface(WifiManager manager) {
+    public static NetworkInterface getWifiNetworkInterface(Object manager) {
         if (ModelUtil.ANDROID_EMULATOR) {
             return getEmulatorWifiNetworkInterface(manager);
         }
-        return getRealWifiNetworkInterface(manager);
+        return getActualNetworkInterface(manager);
     }
 
-    public static NetworkInterface getEmulatorWifiNetworkInterface(WifiManager manager) {
+    public static NetworkInterface getEmulatorWifiNetworkInterface(Object manager) {
         // Return the first network interface that is not loopback
         try {
             List interfaces = Collections.list(NetworkInterface.getNetworkInterfaces());
@@ -169,6 +170,40 @@ public class AndroidNetworkAddressFactory implements NetworkAddressFactory {
         }
         return null;
     }
+    
+    public static NetworkInterface getActualNetworkInterface(Object manager) {
+        if(manager == null)
+        {
+            return getEthernetInterface();
+        }
+        if (manager instanceof WifiManager) {
+            return getRealWifiNetworkInterface ((WifiManager)manager);
+        } else {
+            return getEthernetInterface();
+        }
+        // Return the first network interface that is not loopback
+    }
+        
+    public static NetworkInterface getEthernetInterface()
+    {
+        try {
+            List interfaces = Collections.list(NetworkInterface.getNetworkInterfaces());
+            for (NetworkInterface iface : interfaces) {
+                if (iface.getDisplayName().equals("eth0")) {
+                    List addresses = Collections.list(iface.getInetAddresses());
+                    for (InetAddress address : addresses) {
+                        if (!address.isLoopbackAddress()) {
+                            return iface;
+                        }
+                    }
+                }
+            }
+        } catch (Exception ex) {
+            throw new InitializationException("Could not find emulator's network interface: " + ex, ex);
+        }
+        return null;
+    }
+        
 
     public static NetworkInterface getRealWifiNetworkInterface(WifiManager manager) {
 
diff --git a/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceConfiguration.java b/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceConfiguration.java
index a0e471d..46bb40b 100644
--- a/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceConfiguration.java
+++ b/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceConfiguration.java
@@ -17,7 +17,9 @@
 
 package org.fourthline.cling.android;
 
-import android.net.wifi.WifiManager;
+import java.util.concurrent.Executor;
+import java.util.logging.Logger;
+
 import org.fourthline.cling.DefaultUpnpServiceConfiguration;
 import org.fourthline.cling.binding.xml.DeviceDescriptorBinder;
 import org.fourthline.cling.binding.xml.ServiceDescriptorBinder;
@@ -31,9 +33,6 @@ import org.fourthline.cling.transport.spi.NetworkAddressFactory;
 import org.fourthline.cling.transport.spi.StreamClient;
 import org.fourthline.cling.transport.spi.StreamServer;
 
-import java.util.concurrent.Executor;
-import java.util.logging.Logger;
-
 /**
  * Configuration settings for deployment on Android.
  * <p>
@@ -68,13 +67,13 @@ public class AndroidUpnpServiceConfiguration extends DefaultUpnpServiceConfigura
 
     final private static Logger log = Logger.getLogger(AndroidUpnpServiceConfiguration.class.getName());
 
-    final protected WifiManager wifiManager;
+    final protected Object wifiManager;
 
-    public AndroidUpnpServiceConfiguration(WifiManager wifiManager) {
+    public AndroidUpnpServiceConfiguration(Object wifiManager) {
         this(wifiManager, 0); // Ephemeral port
     }
 
-    public AndroidUpnpServiceConfiguration(WifiManager wifiManager, int streamListenPort) {
+    public AndroidUpnpServiceConfiguration(Object wifiManager, int streamListenPort) {
         super(streamListenPort, false);
 
         this.wifiManager = wifiManager;
diff --git a/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceImpl.java b/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceImpl.java
index bf3525d..5842fce 100644
--- a/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceImpl.java
+++ b/core/src/main/java/org/fourthline/cling/android/AndroidUpnpServiceImpl.java
@@ -17,13 +17,10 @@
 
 package org.fourthline.cling.android;
 
-import android.app.Service;
-import android.content.Context;
-import android.content.Intent;
-import android.content.IntentFilter;
-import android.net.ConnectivityManager;
-import android.net.wifi.WifiManager;
-import android.os.IBinder;
+import java.net.NetworkInterface;
+import java.net.SocketException;
+import java.util.Enumeration;
+
 import org.fourthline.cling.UpnpService;
 import org.fourthline.cling.UpnpServiceConfiguration;
 import org.fourthline.cling.UpnpServiceImpl;
@@ -33,6 +30,14 @@ import org.fourthline.cling.protocol.ProtocolFactory;
 import org.fourthline.cling.registry.Registry;
 import org.fourthline.cling.transport.Router;
 
+import android.app.Service;
+import android.content.Context;
+import android.content.Intent;
+import android.content.IntentFilter;
+import android.net.ConnectivityManager;
+import android.os.IBinder;
+import android.util.Log;
+
 /**
  * Provides a UPnP stack with Android configuration (WiFi network only) as an application service component.
  * <p>
@@ -54,20 +59,39 @@ public class AndroidUpnpServiceImpl extends Service {
     public void onCreate() {
         super.onCreate();
 
-        final WifiManager wifiManager =
-                (WifiManager) getSystemService(Context.WIFI_SERVICE);
+        final Object wifiManager = getSystemService(Context.WIFI_SERVICE);
+        final Object ethernetManager = null;//getSystemService("ethernet");
+        boolean eth = false;
+        try
+        {
+            
+            for(Enumeration list = NetworkInterface.getNetworkInterfaces(); list.hasMoreElements();)
+            {
+                NetworkInterface i = list.nextElement();
+                if(i.getDisplayName().equals("eth0"))
+                {
+                    eth = true;
+                }
+            }
+        }
+        catch(SocketException e)
+        {
+
+        }        
+        final Object manager = (ethernetManager != null)?ethernetManager:(eth ? ethernetManager : wifiManager);
 
         final ConnectivityManager connectivityManager =
                 (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
 
-        upnpService = new UpnpServiceImpl(createConfiguration(wifiManager)) {
+       
+        upnpService = new UpnpServiceImpl(createConfiguration(manager)) {
             @Override
             protected Router createRouter(ProtocolFactory protocolFactory, Registry registry) {
                 AndroidWifiSwitchableRouter router =
                         AndroidUpnpServiceImpl.this.createRouter(
                                 getConfiguration(),
                                 protocolFactory,
-                                wifiManager,
+                                manager,
                                 connectivityManager
                         );
                 if (!ModelUtil.ANDROID_EMULATOR &amp;&amp; isListeningForConnectivityChanges()) {
@@ -83,13 +107,13 @@ public class AndroidUpnpServiceImpl extends Service {
 
     }
 
-    protected AndroidUpnpServiceConfiguration createConfiguration(WifiManager wifiManager) {
+    protected AndroidUpnpServiceConfiguration createConfiguration(Object wifiManager) {
         return new AndroidUpnpServiceConfiguration(wifiManager);
     }
 
     protected AndroidWifiSwitchableRouter createRouter(UpnpServiceConfiguration configuration,
                                                       ProtocolFactory protocolFactory,
-                                                      WifiManager wifiManager,
+                                                      Object wifiManager,
                                                       ConnectivityManager connectivityManager) {
         return new AndroidWifiSwitchableRouter(configuration, protocolFactory, wifiManager, connectivityManager);
     }
diff --git a/core/src/main/java/org/fourthline/cling/android/AndroidWifiSwitchableRouter.java b/core/src/main/java/org/fourthline/cling/android/AndroidWifiSwitchableRouter.java
index 1d7af68..eeb9f92 100644
--- a/core/src/main/java/org/fourthline/cling/android/AndroidWifiSwitchableRouter.java
+++ b/core/src/main/java/org/fourthline/cling/android/AndroidWifiSwitchableRouter.java
@@ -17,12 +17,12 @@
 
 package org.fourthline.cling.android;
 
-import android.content.BroadcastReceiver;
-import android.content.Context;
-import android.content.Intent;
-import android.net.ConnectivityManager;
-import android.net.NetworkInfo;
-import android.net.wifi.WifiManager;
+import java.net.NetworkInterface;
+import java.net.SocketException;
+import java.util.Collections;
+import java.util.List;
+import java.util.logging.Logger;
+
 import org.fourthline.cling.UpnpServiceConfiguration;
 import org.fourthline.cling.model.ModelUtil;
 import org.fourthline.cling.protocol.ProtocolFactory;
@@ -30,7 +30,12 @@ import org.fourthline.cling.transport.Router;
 import org.fourthline.cling.transport.SwitchableRouterImpl;
 import org.fourthline.cling.transport.spi.InitializationException;
 
-import java.util.logging.Logger;
+import android.content.BroadcastReceiver;
+import android.content.Context;
+import android.content.Intent;
+import android.net.ConnectivityManager;
+import android.net.NetworkInfo;
+import android.net.wifi.WifiManager;
 
 /**
  * Switches the network transport layer on/off by monitoring WiFi connectivity.
@@ -51,24 +56,37 @@ public class AndroidWifiSwitchableRouter extends SwitchableRouterImpl {
             if (!intent.getAction().equals(ConnectivityManager.CONNECTIVITY_ACTION)) return;
             NetworkInfo wifiInfo = getConnectivityManager().getNetworkInfo(ConnectivityManager.TYPE_WIFI);
             // We can't listen to "is available" or simply "is switched on", we have to make sure it's connected
-            if (!wifiInfo.isConnected()) {
-                log.info("WiFi state changed, trying to disable router");
-                disable();
-            } else {
-                log.info("WiFi state changed, trying to enable router");
-                enable();
+            NetworkInterface ethernet = null;
+            try {
+                List interfaces = Collections.list(NetworkInterface.getNetworkInterfaces());
+                for (NetworkInterface iface : interfaces) {
+                    if (iface.getDisplayName().equals("eth0")) {
+                        ethernet = iface;
+                        break;
+                    }
+                }
+                if (!wifiInfo.isConnected() &amp;&amp; (ethernet != null) &amp;&amp; !ethernet.isUp()) {
+                    log.info("WiFi state changed, trying to disable router");
+                    disable();
+                } else {
+                    log.info("WiFi state changed, trying to enable router");
+                    enable();
+                }
+            } 
+            catch (SocketException sx)
+            {
             }
         }
     };
 
-    final private WifiManager wifiManager;
+    final private Object manager;
     final private ConnectivityManager connectivityManager;
     private WifiManager.MulticastLock multicastLock;
 
     public AndroidWifiSwitchableRouter(UpnpServiceConfiguration configuration, ProtocolFactory protocolFactory,
-                                       WifiManager wifiManager, ConnectivityManager connectivityManager) {
+            Object manager, ConnectivityManager connectivityManager) {
         super(configuration, protocolFactory);
-        this.wifiManager = wifiManager;
+        this.manager = manager;
         this.connectivityManager = connectivityManager;
 
         // Let's not wait for the first "wifi switched on" broadcast (which might be late on
@@ -85,7 +103,12 @@ public class AndroidWifiSwitchableRouter extends SwitchableRouterImpl {
     }
 
     protected WifiManager getWifiManager() {
-        return wifiManager;
+        if(manager == null) return null;
+        if (manager instanceof WifiManager) {
+            return (WifiManager)manager;
+        } else {
+            return null;
+        }
     }
 
     protected ConnectivityManager getConnectivityManager() {
@@ -99,8 +122,10 @@ public class AndroidWifiSwitchableRouter extends SwitchableRouterImpl {
             boolean enabled;
             if ((enabled = super.enable())) {
                 // Enable multicast on the WiFi network interface, requires android.permission.CHANGE_WIFI_MULTICAST_STATE
-                multicastLock = getWifiManager().createMulticastLock(getClass().getSimpleName());
-                multicastLock.acquire();
+                if (getWifiManager() != null) {
+                    multicastLock = getWifiManager().createMulticastLock(getClass().getSimpleName());
+                    multicastLock.acquire();
+                }
             }
             return enabled;
         } finally {
</code>
