---
layout: post
title: Animating Toolbar Titles
categories: Walkthroughs
description: When you change a title in a toolbar, it just flashes to a new value. I'm going to show you how to create an animation for transitioning between values.
tags: [Android, Animations, Walkthrough]
image:
  feature: nebula.jpg
comments: true
share: true
---

## Overview
I'm sure by now, everyone has used the support library's `Toolbar` element. Inside of this element are things like the `ActionMenu` and Title. However, since this is a packaged element. We don't have access to the child views of this view. So I am going to show you how to get the title and attach animations to it for smoothly transitioning between title changes.

## Toolbar
Now, in order for us to use the `Toolbar` we first need to have one in the layout. So make sure you have the style set to no actionbar, and make sure you have a varient of the following in your layout:

{% highlight xml %}
<android.support.design.widget.AppBarLayout
    android:id="@+id/app_bar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.MarketBot.Toolbar.ItemToolbar"
    android:elevation="4dp">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Light"/>

</android.support.design.widget.AppBarLayout>
{% endhighlight %}

Now when you start your activity, you won't need to do anything different. However, there is one thing you should take into account. If you set your title to an empty string, then the `TextView` will be removed from the `Toolbar` so if you plan on animating your title, you should set your title to ' ' if you want to have an empty title.

## Finding the TextView
Seeing as how we don't actually hae access to the children of the `Toolbar` we will have to do a pretty nasty search for it. What you will need to do is iterate over each child of the `Toolbar` until you find a `TextView`. You need to do this on the `Toolbar` not the support `ActionBar` you are setting the `Toolbar` to when you start an activity! It should look something like this:

{% highlight java %}
private View getToolbarTitle() {
    int childCount = toolbar.getChildCount();
    for (int i = 0; i < childCount; i++) {
        View child = toolbar.getChildAt(i);
        if (child instanceof TextView) {
            return child;
        }
    }

    return new View(this);
}
{% endhighlight %}

## Animating Title Change
When you finally have the view for your title, you can just run an animation like you usually would. For this example I just set up an `AlphaAnimation` that fades the title out of view, updates the value, then fades it back in. Here is what it looks like:

{% highlight java %}
private void animateTitleChange(String newTitle) {
    final View view = getToolbarTitle();

    if (view instanceof TextView) {
        AlphaAnimation fadeOut = new AlphaAnimation(1f, 0f);
        fadeOut.setDuration(250);
        fadeOut.setAnimationListener(new Animation.AnimationListener() {
            @Override
            public void onAnimationStart(Animation animation) {

            }

            @Override
            public void onAnimationEnd(Animation animation) {
                actionBar.setTitle(newTitle);
                
                AlphaAnimation fadeIn = new AlphaAnimation(0f, 1f);
                fadeIn.setDuration(250);
                view.startAnimation(fadeIn);
            }

            @Override
            public void onAnimationRepeat(Animation animation) {

            }
        });

        view.startAnimation(fadeOut);
    }
}
{% endhighlight %}

Keep in mind that the `TextView` won't be in the `Toolbar` unless it has a value, and you can't count on it always being in the same place. Otherwise, enjoy animating the title to your hearts content.