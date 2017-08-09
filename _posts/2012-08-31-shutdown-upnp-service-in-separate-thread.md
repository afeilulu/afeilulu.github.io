---
layout: post
title: Shutdown upnp service in separate thread
date: 2012-08-31 11:36
author: afeilulu
comments: true
categories: [Android, program]
---
When i use cling stack for android upnp application development,an error called "NetworkOnMainThreadException" will occur when shutdown the upnp service.So we need to shutdown it in a separate thread through overiding "Destroy" method.

<code>
public class BrowserUpnpService extends AndroidUpnpServiceImpl {
	
	@Override
    protected AndroidUpnpServiceConfiguration createConfiguration(WifiManager wifiManager) {
        return new AndroidUpnpServiceConfiguration(wifiManager) {

        	/* This is optimization for Android device
        	 * 1.set registry maintenance interval time
        	 * 2.If you are not writing a control point but a server application, you can 
        	 *   return null in the getExclusiveServiceTypes() method. This will disable 
        	 *   discovery completely, now all device and service advertisements are dropped 
        	 *   as soon as they are received.
        	 * 3.This is a control point.So,we need selective discovery of UPnP devices.
        	 *    If instead we return an empty array (the default behavior), all services 
        	 *    and devices will be discovered and no advertisements will be dropped.
        	 */
        	
            /* The only purpose of this class is to show you how you'd
             * configure the AndroidUpnpServiceImpl in your application:
             */
           @Override
           public int getRegistryMaintenanceIntervalMillis() {
               return 7000;
           }

           @Override
           public ServiceType[] getExclusiveServiceTypes() {
               return new ServiceType[] {
                       new UDAServiceType("RenderingControl"),
                       new UDAServiceType("AVTransport")
               };
           }

        };
    }

	@Override
	public void onDestroy() {
//		super.onDestroy();
		
		new Thread(new Runnable(){

			@Override
			public void run() {
				if (!ModelUtil.ANDROID_EMULATOR &amp;&amp; isListeningForConnectivityChanges()){
		  		  unregisterReceiver(((AndroidWifiSwitchableRouter) upnpService.getRouter()).getBroadcastReceiver());
				}
			  		  
				new Shutdown().execute(upnpService);
				
			}}).run();
	}
		
	/**
	 * Do shutdown in separate thread else android.os.NetworkOnMainThreadException
	 */ 
	class Shutdown extends AsyncTask{
	    @Override
	    protected Void doInBackground(UpnpService... svcs) {
	      UpnpService svc = svcs[0];
	      if (null != svc) {
	    	  try{
		    	 svc.shutdown();
	    	  }
	      	  catch(java.lang.IllegalArgumentException ex){
	      		  // java.lang.IllegalArgumentException: Receiver not registered: org.teleal.cling.android.AndroidWifiSwitchableRouter$1@417b3c80
	      		  ex.printStackTrace();
	      	  }
	      }
	      return null;
	    }
	}


}
</code>
