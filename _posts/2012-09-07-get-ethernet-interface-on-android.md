---
layout: post
title: Get ethernet interface on android
date: 2012-09-07 09:57
author: afeilulu
comments: true
categories: [Android, program]
---
On some android STB devices, ethernet interface is comeing with it.Here is the way how you can get information about it without root permission.

The "ethernet" will be null,when there is no ethernet interface on device and when the ethernet interface is offline/uncheck.
Keep it mind that it need android api level 9 to run.


<code>
ConnectivityManager connectivityManager =
                (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo wifiInfo = connectivityManager.getNetworkInfo(ConnectivityManager.TYPE_WIFI);
        // We can't listen to "is available" or simply "is switched on", we have to make sure it's connected            
        NetworkInterface ethernet = null;
        try {
            List interfaces = Collections.list(NetworkInterface.getNetworkInterfaces());
            for (NetworkInterface iface : interfaces) {
                if (iface.getDisplayName().equals("eth0")) {
                    ethernet = iface;
                    break;
                }
            }
            Log.d(TAG, "wifiInfo.isConnected() = " + wifiInfo.isConnected());
            Log.d(TAG, "ethernet = " + ethernet);
            if (ethernet != null)
            	Log.d(TAG,"ethernet.isUp() = " + ethernet.isUp());
            
            if (!wifiInfo.isConnected() &amp;&amp; (ethernet != null) &amp;&amp; !ethernet.isUp()) {
                Toast.makeText(this, "WiFi state changed, trying to disable router", Toast.LENGTH_SHORT).show();
            } else {
            	Toast.makeText(this, "WiFi state changed, trying to enable router", Toast.LENGTH_SHORT).show();
            }
        } catch (SocketException sx) {
        }
</code>

