---
layout: post
title: Create Material Design Navigation Drawer
categories: Walkthroughs
description: Walkthrough on how to create a truely material design compliant Naviagation Drawer for Android.
tags: [Android, 5.0, Design, Drawer, Google, Material, Walkthrough]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

### Overview

Currently at the moment there isn't really a guide on how to get a fully material design compliant drawer on android 5.0. So this is going to be a walkthrough on how to do exactly that.

### Setup

Your targeting api should probably be v21+ (or android 5.0+) and you can either do `no activities` or a `blank activity`. You will want to immediately check out your build.gradle and make sure you have the `appcompat` dependency is in there. (Note: this is usually automatically included for you).

{% highlight groovy %}

// + is where the version is (or leave it so it auto updates for you)
compile 'com.android.support:appcompat-v7:+'

{% endhighlight %}

Along with making sure your main theme is an `AppCompat` theme, not the device default theme.Inside of that theme make sure you have `windowActionBar` set to false.

{% highlight xml %}
<item name="windowActionBar">false</item>
{% endhighlight %}

### Replacing the ActionBar

Lets start with how to get the drawer out from under the action bar. By default when you add a drawer to your application it will appear under your action bar. To rectify this, you need to use a toolbar instead of the default `ActionBar`. When you added `windowActionBar` to your theme, you where actually turning that actionbar off. To get back that actionBar we need to change the activity to `AppCompatActivity`.

{% highlight java %}

public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}

{% endhighlight %}

Now that our activity extends `AppCompatActivity` we need to go and add a `Toolbar` to this activity's layout. It should look something like this:

{% highlight xml %}

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:background="?attr/colorPrimary"
        android:elevation="4dp"
        android:id="@+id/main_toolbar"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:minHeight="?attr/actionBarSize"
        app:theme="@style/ToolbarOverlay"
        app:popupTheme="@style/PopupOverlay">

    </android.support.v7.widget.Toolbar>

    <FrameLayout
        android:id="@+id/content_frame"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">

    </FrameLayout>

</LinearLayout>

{% endhighlight %}

We have the `Toolbar` in our layout, but now we need to hook it up to your activity. So add this to your `onCreate` method.

{% highlight java %}

Toolbar toolbar = (Toolbar) findViewById(R.id.main_toolbar);
setSupportActionBar(toolbar);

{% endhighlight %}

The last thing that is missing for our `Toolbar` is the styles. This consists of things like background color, popover color, height, button styles, etc. The next style is for v21 *only*. You'll want use this in a `styles-v21.xml` file.

{% highlight xml %}

<resources>

    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="colorPrimary">@color/main_blue</item>
        <item name="colorPrimaryDark">@color/main_blue_dark</item>
        <item name="colorAccent">@color/main_blue</item>
        <item name="colorControlHighlight">@color/main_blue</item>
        <item name="android:windowNoTitle">true</item>
        <item name="drawerArrowStyle">@style/DrawerArrowStyle</item>
        <item name="windowActionModeOverlay">true</item>
    </style>

    <!--used to have the arrow animation for the hamburger button-->
    <style name="DrawerArrowStyle" parent="Widget.AppCompat.DrawerArrowToggle">
        <item name="spinBars">true</item>
        <item name="color">@android:color/white</item>
    </style>

    <!--styles the toolbar to this color and font color-->
    <style name="ToolbarOverlay" parent="Theme.AppCompat.Light">
        <item name="android:textColorPrimary">@color/white</item>
        <item name="colorPrimary">@color/main_blue</item>
        <item name="android:textColorSecondary">@color/white</item>
    </style>

    <!--this is used for styling the popup like from the three dots on the right-->
    <style name="PopupOverlay" parent="Theme.AppCompat.Light">

    </style>

</resources>

{% endhighlight %}

### Setup the Navigation Drawer

First, we need to add in the drawer to our layout. So make your main activity's layout look something like this:

{% highlight xml %}

<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:animateLayoutChanges="true">

    <android.support.v4.widget.DrawerLayout
        android:id="@+id/drawer_layout"
        android:fitsSystemWindows="true"
        android:elevation="5dp"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <!-- The main content view -->
        <LinearLayout
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <android.support.v7.widget.Toolbar
                android:background="?attr/colorPrimary"
                android:elevation="4dp"
                android:id="@+id/main_toolbar"
                android:layout_height="wrap_content"
                android:layout_width="match_parent"
                android:minHeight="?attr/actionBarSize"
                app:theme="@style/ToolbarOverlay"
                app:popupTheme="@style/PopupOverlay">

            </android.support.v7.widget.Toolbar>

            <FrameLayout
                android:id="@+id/content_frame"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent">

            </FrameLayout>


        </LinearLayout>

        <!-- The navigation drawer -->
        <ListView
            android:id="@+id/drawer_listview"
            android:layout_width="fill_parent"
            android:layout_height="match_parent"
            android:minHeight="?android:attr/listPreferredItemHeight"
            android:fitsSystemWindows="true"
            android:divider="@null"
            android:dividerHeight="0dp"
            android:headerDividersEnabled="true">

        </ListView>

    </android.support.v4.widget.DrawerLayout>

</RelativeLayout>

{% endhighlight %}

We have the layout, but now we need to hook it up inside the activity. So we need to add in handling back button, configuration changes, and toggling. You main activity should start to look something like this:

{% highlight java %}

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Toolbar toolbar = (Toolbar) findViewById(R.id.main_toolbar);
        setSupportActionBar(toolbar);

        mDrawerList = (ListView) findViewById(R.id.drawer_listview);
        mDrawerLayout = (DrawerLayout) findViewById(R.id.drawer_layout);
        mDrawerToggle = new ActionBarDrawerToggle(this, mDrawerLayout, toolbar,
                R.string.open, R.string.close);
        mDrawerToggle.setDrawerIndicatorEnabled(true);
        mDrawerLayout.setDrawerListener(mDrawerToggle);
    }

    @Override
    protected void onPostCreate(Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        mDrawerToggle.syncState();
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        mDrawerToggle.onConfigurationChanged(newConfig);
    }

    @Override
    public void onBackPressed() {
        if(mDrawerLayout.isDrawerOpen(Gravity.START|Gravity.LEFT)){
            mDrawerLayout.closeDrawers();
            return;
        }

        super.onBackPressed();
    }
}

{% endhighlight %}

With this, your navigation drawer will now but up to the bottom of your status bar. However, in the material design principles they make it clear they want a transparent status bar to draw **OVER** the drawer.

### Making the Drawer full Application Height
**If you are making a non 5.0 app this section isn't required because it isn't supported until v21**

First you will need to set up a couple of properties in a special styles-v21.xml file so that we can force it to be full screen. You should only need `windowDrawsSystemBarBackgrounds` but I like to set a couple other transitions and stuff so set these:

{% highlight xml %}

<resources>

    <!--added specifically for handling material design transitions and full window view-->
    <style name="AppTheme" parent="AppTheme.Base">
        <item name="android:windowDrawsSystemBarBackgrounds">true</item>
        <item name="android:statusBarColor">#33000000</item>
        <item name="android:windowContentTransitions">true</item>
        <item name="android:windowAllowEnterTransitionOverlap">true</item>
        <item name="android:windowAllowReturnTransitionOverlap">true</item>
        <item name="android:windowSharedElementEnterTransition">@android:transition/move</item>
        <item name="android:windowSharedElementExitTransition">@android:transition/move</item>
    </style>

    <style name="AppTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="windowActionBar">false</item>
        <item name="colorPrimary">@color/main_blue</item>
        <item name="colorPrimaryDark">@color/main_blue_dark</item>
        <item name="colorAccent">@color/main_blue</item>
        <item name="colorControlHighlight">@color/transparent_main_blue</item>
        <item name="android:windowNoTitle">true</item>
        <item name="drawerArrowStyle">@style/DrawerArrowStyle</item>
        <item name="windowActionModeOverlay">true</item>
    </style>

    <style name="DrawerArrowStyle" parent="Widget.AppCompat.DrawerArrowToggle">
        <item name="spinBars">true</item>
        <item name="color">@android:color/white</item>
    </style>

    <style name="ToolbarOverlay" parent="Theme.AppCompat.Light">
        <item name="android:textColorPrimary">@color/white</item>
        <item name="colorPrimary">@color/main_blue</item>
        <item name="android:textColorSecondary">@color/white</item>
    </style>

    <style name="PopupOverlay" parent="Theme.AppCompat.Light">

    </style>

</resources>

{% endhighlight %}

With those styles, you just need to make sure the `DrawerLayout` in your main activity has the `fitsSystemWindow` attribute set to true. This should make the drawer go your status bar correctly. There is one problem though. Since the status bar is being drawn there android assumes you don't want your content to start until under your status bar. So if you put an image on the top for the header (like Google does), you would get a white bar above it. So to fix this you need to add in a custom layout called `ScrimInsetsFrameLayout` which insets the view to get rid of that white bar. That class can be found in this gist:

{% include JB/gist gist_id="a753888dd64668ab3efa" %}
{% include JB/gist gist_id="a753888dd64668ab3efa" gist_file="jekyll-bootstrap.js" %}

Now just go and update the layout for your activity to use this layout to get a result somewhat like this:

{% highlight xml %}

<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:animateLayoutChanges="true">

    <android.support.v4.widget.DrawerLayout
        android:id="@+id/drawer_layout"
        android:fitsSystemWindows="true"
        android:elevation="5dp"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <!-- The main content view -->
        <LinearLayout
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <android.support.v7.widget.Toolbar
                android:background="?attr/colorPrimary"
                android:elevation="4dp"
                android:id="@+id/main_toolbar"
                android:layout_height="wrap_content"
                android:layout_width="match_parent"
                android:minHeight="?attr/actionBarSize"
                app:theme="@style/ToolbarOverlay"
                app:popupTheme="@style/PopupOverlay">

            </android.support.v7.widget.Toolbar>

            <FrameLayout
                android:id="@+id/content_frame"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent">

            </FrameLayout>


        </LinearLayout>


        <!-- The navigation drawer -->
        <com.w9jds.EveProfiler.Widgets.ScrimInsetsFrameLayout
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/scrimInsetsFrameLayout"
            android:layout_width="308dp"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            android:elevation="10dp"
            android:background="@color/white"
            android:fitsSystemWindows="true"
            app:insetForeground="#4000">

            <ListView
                android:id="@+id/drawer_listview"
                android:layout_width="fill_parent"
                android:layout_height="match_parent"
                android:minHeight="?android:attr/listPreferredItemHeight"
                android:fitsSystemWindows="true"
                android:divider="@null"
                android:dividerHeight="0dp"
                android:headerDividersEnabled="true">

            </ListView>

        </com.fatrussell.wordoftheday.Widgets.ScrimInsetsFrameLayout>

    </android.support.v4.widget.DrawerLayout>

</RelativeLayout>

{% endhighlight %}

and now you have a drawer that slides under the status bar exactly how the drawer is depicted in the material design guidelines!
