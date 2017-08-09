---
layout: post
title: Google Analytics in Android app
date: 2012-08-18 12:11
author: afeilulu
comments: true
categories: [Android, program]
---
Original posted at <a href="http://stackoverflow.com/questions/3216692/google-analytics-in-android-app-dealing-with-multiple-activities?rq=1">here</a>.It is an excellent solution.

The problem with calling start()/stop() in every activity (as suggested by Christian) is that it results in a new "visit" for every activity your user navigates to. If this is okay for your usage, then that's fine, however, it's not the way most people expect visits to work. For example, this would make comparing android numbers to web or iphone numbers very difficult, since a "visit" on the web and iphone maps to a session, not a page/activity.

The problem with calling start()/stop() in your Application is that it results in unexpectedly long visits, since Android makes no guarantees to terminate the application after your last activity closes. In addition, if your app does anything with notifications or services, these background tasks can start up your app and result in "phantom" visits. <em>UPDATE: stefano properly points out that <a href="http://developer.android.com/intl/fr/reference/android/app/Application.html#onTerminate%28%29">onTerminate()</a> is never called on a real device, so there's no obvious place to put the call to stop().</em>

The problem with calling start()/stop() in a single "main" activity (as suggested by Aurora) is that there's no guarantee that the activity will stick around for the duration that your user is using your app. If the "main" activity is destroyed (say to free up memory), your subsequent attempts to write events to GA in other activities will fail because the session has been stopped.

In addition, there's a bug in Google Analytics up through at least version 1.2 that causes it to keep a strong reference to the context you pass in to start(), preventing it from ever getting garbage collected after its destroyed. Depending on the size of your context, this can be a sizable memory leak.

The memory leak is easy enough to fix, it can be solved by calling start() using the Application instead of the activity instance itself. The <a href="http://code.google.com/mobile/analytics/docs/android/#startingTheTracker">docs</a> should probably be updated to reflect this.

eg. from inside your Activity:
<pre><code>// Start the tracker in manual dispatch mode...
tracker.start("UA-YOUR-ACCOUNT-HERE", getApplication() );
</code></pre>
instead of
<pre><code>// Start the tracker in manual dispatch mode...
tracker.start("UA-YOUR-ACCOUNT-HERE", this ); // BAD
</code></pre>
Regarding when to call start()/stop(), you can implement a sort of manual reference counting, incrementing a count for each call to Activity.onCreate() and decrementing for each onDestroy(), then calling GoogleAnalyticsTracker.stop() when the count reaches zero.

The new <a href="http://analytics.blogspot.com/2011/12/google-analytics-enhancements-for.html">EasyTracker</a> library from Google will take care of this for you.

Alternately, if you can't subclass the EasyTracker activities, you can implement this manually yourself in your own activity base class:
<pre><code>public abstract class GoogleAnalyticsActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Need to do this for every activity that uses google analytics
        GoogleAnalyticsSessionManager.getInstance(getApplication()).incrementActivityCount();
    }

    @Override
    protected void onResume() {
        super.onResume();

        // Example of how to track a pageview event
        GoogleAnalyticsTracker.getInstance().trackPageView(getClass().getSimpleName());
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();

        // Purge analytics so they don't hold references to this activity
        GoogleAnalyticsTracker.getInstance().dispatch();

        // Need to do this for every activity that uses google analytics
        GoogleAnalyticsSessionManager.getInstance().decrementActivityCount();
    }

}

public class GoogleAnalyticsSessionManager {
    protected static GoogleAnalyticsSessionManager INSTANCE;

    protected int activityCount = 0;
    protected Integer dispatchIntervalSecs;
    protected String apiKey;
    protected Context context;

    /**
     * NOTE: you should use your Application context, not your Activity context, in order to avoid memory leaks.
     */
    protected GoogleAnalyticsSessionManager( String apiKey, Application context ) {
        this.apiKey = apiKey;
        this.context = context;
    }

    /**
     * NOTE: you should use your Application context, not your Activity context, in order to avoid memory leaks.
     */
    protected GoogleAnalyticsSessionManager( String apiKey, int dispatchIntervalSecs, Application context ) {
        this.apiKey = apiKey;
        this.dispatchIntervalSecs = dispatchIntervalSecs;
        this.context = context;
    }

    /**
     * This should be called once in onCreate() for each of your activities that use GoogleAnalytics.
     * These methods are not synchronized and don't generally need to be, so if you want to do anything
     * unusual you should synchronize them yourself.
     */
    public void incrementActivityCount() {
        if( activityCount==0 )
            if( dispatchIntervalSecs==null )
                GoogleAnalyticsTracker.getInstance().start(apiKey,context);
            else
                GoogleAnalyticsTracker.getInstance().start(apiKey,dispatchIntervalSecs,context);

        ++activityCount;
    }

    /**
     * This should be called once in onDestrkg() for each of your activities that use GoogleAnalytics.
     * These methods are not synchronized and don't generally need to be, so if you want to do anything
     * unusual you should synchronize them yourself.
     */
    public void decrementActivityCount() {
        activityCount = Math.max(activityCount-1, 0);

        if( activityCount==0 )
            GoogleAnalyticsTracker.getInstance().stop();
    }

    /**
     * Get or create an instance of GoogleAnalyticsSessionManager
     */
    public static GoogleAnalyticsSessionManager getInstance( Application application ) {
        if( INSTANCE == null )
            INSTANCE = new GoogleAnalyticsSessionManager( ... ,application);
        return INSTANCE;
    }

    /**
     * Only call this if you're sure an instance has been previously created using #getInstance(Application)
     */
    public static GoogleAnalyticsSessionManager getInstance() {
        return INSTANCE;
    }
}</code></pre>
