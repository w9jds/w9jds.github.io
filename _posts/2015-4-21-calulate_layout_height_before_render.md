---
layout: post
title: Calculate Layout Dimensions Before Render
categories:
description: Calculate the dimensions of a layout before the system renders it to the user, or use it to get the height of a layout that isn't fully visible.
tags: [Android, Layouts, Animations]
image:
  feature: spacetwo.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

## Overview

Usually when you want to make an animation to show some hidden content, or draw a layout onto a canvas you need to know the dimensions. The issue is that you can't really get them until that view has been rendered to the user. I am going to show you a way to do this without having to render it to the user. I have used this to get specific heights for one item in a `ListView` or full height of a `ListView` allowing me to expand and collapse from one item to all of them. I also used this to replicate the vignette feature from Google Glass to help draw specific things into the top right of an image.

## How it works

We are going to use the built-in system for `Views` called `MeasureSpec`. Basically we are going to give the view parameters on how we would want it measured, and it would then go and get the real dimensions for this device. I will give you two examples of this.

The first one is measuring out a layout that we know exactly what the height and width we want it to be set to.

{% highlight java %}

layout.measure(View.MeasureSpec.makeMeasureSpec(640, View.MeasureSpec.EXACTLY),
        View.MeasureSpec.makeMeasureSpec(360, View.MeasureSpec.EXACTLY));

{% endhighlight %}

But say we want to get the dimensions of something that we don't know the exact values for since the text can be multiple lines. We can then say something like this.

{% highlight java %}

layout.measure(View.MeasureSpec.makeMeasureSpec(listView.getWidth(), View.MeasureSpec.AT_MOST),
        View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED));

{% endhighlight %}

In this view I'm using the `ListView` to set the largest width that it can go, so that the text wraps correctly when it measure the layout. This allows me to get an exact value for what the height would be if it were rendered.

Since this view isn't rendered yet or is hidden, your `getHeight()` and `getWidth()` values with either be nothing or just off. So to get these values you just measured you would use this:

{% highlight java %}

// get the height
layout.getMeasuredHeight();
// get the width
layout.getMeasuredWidth();

{% endhighlight %}
