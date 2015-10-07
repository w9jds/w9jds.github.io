---
layout: post
title: Using Lollipop Activity Transitions
categories: Walkthroughs
description: Going over a few things you can do with the new CoordinatedLayout that came out with the new Design Library.
tags: [Android, Design, Material, Support, Walkthrough]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
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
`CoordinatedLayout` also allows you to use an achor feature. This anchors `FloatingActionButtons` to a specific location on your screen so when a `SnackBar` appears, the button slides up to stay in the position. This is also a pretty simple item to implement. All it requires are a couple of attributes on your `FloatingActionButton`. Below is a example of how you would anchor it to the bottom right hand side like it is for standard Material Design.

{% highlight xml %}



{% endhighlight %}

This also works if you want to attach it to the bottom of an action bar that collapses. Instead of having it move up from an item appearing, it follows the bottom of the action bar and disappears when the action bar is completely collapsed.