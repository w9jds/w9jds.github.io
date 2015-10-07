---
layout: post
title: Using The Design Library's CoordinatedLayout
categories: Walkthroughs
description: Going over a few things you can do with the new CoordinatedLayout that came out with the new Design Library.
tags: [Android, Design, Material, Support, Walkthrough]
image:
  feature: heart_nebula.jpg
comments: true
share: true
---

##What is CoordinatedLayout
Recently Google released a new Support library called Design. This library was specifically designed to help the user create Material Design look and feel elements to add into their application without having to create their own custom views. With an added bonus is that it supports lower versions of Android. Inside of this library we got a handful of things, but probably the most powerful tool they released was the `CoordinatedLayout`. This layout allows us to create context aware elements inside of our layouts so that they can behave in a specific way based on different criteria. These are `Behaviors`, and can be specified from a set that Google already made, or by making your own. Some of the animations that `CoordinatedLayout` make possible are: hiding the `FloatingActionButton` on scroll, hiding the `ActionBarLayout` on scroll, swiping away `SnackBar`s,  `FloatingActionButton` moving up when `SnackBar` appears, etc.

It is extremely import to remember that the behavior is REQUIRED to be attached to the direct child of the `CoordinatedLayout`.

##How to use it
`CoordinatedLayout` is actually extremely easy to use. All it is, is a standard layout that you include in one of your layout xml files. It will look a bit like this:

{% highlight xml %}

<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/main_content"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <FrameLayout
        android:id="@+id/content_frame"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">

    </FrameLayout>
    
</android.support.design.widget.CoordinatorLayout>

{% endhighlight %}

As you can see, it look a lot like a `LinearLayout` or a `FrameLayout`. However, look at the `FrameLayout` we have in there. That `layout_behavior` option is specifically for direct children of `CoordinatedLayout`. This specific behavior is used to collapse things like an Action Bar or `FloatingActionButton`. Keep in mind though, for the scrolling to work you will either need a `RecyclerView` or a `NestedScrollView` otherwise this behavior will never get fired. 

I can't stress enough that the behavior has to be set to the direct child element of `CoordinatedLayout`. Even if the element you are putting the tag on doesn't even scroll (like above) it still goes on that `FrameLayout` even though I will be loading a `RecyclerView` in later. This even works if you are loading a `ViewPager` with `Fragments` containing `RecyclerViews` into that `FrameLayout`. Another good example of this is if you are using a `SwipeRefreshLayout`. Since `SwipeRefreshLayout` is the child of `CoordinatedLayout` you need to put it on there. Then the `RecyclerView` contained in it will have the scroll events fire the behavior.

##Other uses
`CoordinatedLayout` also allows you to use an achor feature. This anchors `FloatingActionButtons` to a specific location on your screen so when a `SnackBar` appears, the button slides up to stay in the position. This is also a pretty simple item to implement. All it requires are a couple of attributes on your `FloatingActionButton`. Below is a example of how you would anchor it to the bottom right hand side like it is for standard Material Design. This example will anchor it to the list, so that when you scroll it hides:

{% highlight xml %}

<android.support.design.widget.FloatingActionButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:src="@drawable/ic_done"
    app:layout_anchor="@id/content_list"
    app:layout_anchorGravity="bottom|end" />

{% endhighlight %}

This also works if you want to attach it to the bottom of an action bar that collapses. Instead of having it move up from an item appearing, it follows the bottom of the action bar and disappears when the action bar is completely collapsed.

This also allows for `CollapsingToolbarLayout`. This layout is effected by the scrolling behavior shown above, and allows for the action bar to expand (moving the title with it) and even switch into a parallax mode where the background turns into an image. A good example of this effect is the native Android application. Here is a snippet of xml to show you how to do a parallax version of the Toolbar.

{% highlight xml %}

<android.support.design.widget.AppBarLayout
    android:id="@+id/app_bar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

    <android.support.design.widget.CollapsingToolbarLayout
        android:id="@+id/collapsing_toolbar"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_scrollFlags="scroll|exitUntilCollapsed"
        android:fitsSystemWindows="true"
        app:contentScrim="?attr/colorPrimary"
        app:expandedTitleMarginStart="48dp"
        app:expandedTitleMarginEnd="64dp">

        <ImageView
            app:layout_scrollFlags="scroll|enterAlways|enterAlwaysCollapsed"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:scaleType="centerCrop"
            app:layout_collapseMode="parallax"
            android:minHeight="250dp"/>

        <android.support.v7.widget.Toolbar
            android:id="@+id/main_toolbar"
            android:layout_height="?attr/actionBarSize"
            android:layout_width="match_parent"
            android:background="@android:color/transparent"
            android:theme="@style/ToolbarOverlay"
            app:layout_scrollFlags="scroll|enterAlways"/>

    </android.support.design.widget.CollapsingToolbarLayout>

</android.support.design.widget.AppBarLayout>

{% endhighlight %}

And then what you can do, is add in the anchored `FloatingActionButton` so that it attaches to the bottom of the action bar. This usually means that the top and the content are related and the action inside of the `FloatingActionButton` effects both areas. This usage is better outlined in the Material Design guidlines here: [Floating Action Button Guidlines](https://goo.gl/aohN7k). Just add this button to the end of the `CoordinatedLayout` that the above `AppBarLayout` is in and you will have the same effect.

{% highlight xml %}

<android.support.design.widget.FloatingActionButton
    android:layout_height="wrap_content"
    android:layout_width="wrap_content"
    app:fabSize="normal"
    app:layout_anchor="@id/app_bar"
    app:layout_anchorGravity="bottom|end"
    android:layout_marginEnd="16dp"/>

{% endhighlight %}

##Overview
`CoordinatedLayout`s are extremely powerful and really help make your applications fluid and nice to use. The support design library really adds loads of functionality to our applications and all it really takes to implement most of them is just a little xml. All of this functionality is packed into a single support library and can be added into your project by adding this to your dependencies in gradle:

{% highlight groovy %}

compile 'com.android.support:design:23.0.1'

{% endhighlight %}