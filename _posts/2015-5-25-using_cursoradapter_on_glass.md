---
layout: post
title: Using Cursor Adapters on Google Glass CardScrollView
categories: Walkthroughs
description: Originally CardScrollView's on the GDK only allow CardScrollAdapters. I am going to show you how to use a CursorAdapter for them.
tags: [Google Glass, GDK, CursorAdapter, CardScrollView]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

## Overview

Out of the box `CardScrollView` in the Google Glass GDK will only allow adapters of type `CardScrollAdapter` to be set on them. So I am going to share with you two custom classes that allow you to use a CursorAdapter on any of your `CardScrollViews`.

Note these are exact reuses of the `CursorAdapter` that is used in the standard Android SDK so I am not reinventing the wheel here. All I did was tweak them to allow for use with the GDK `CardScrollView`.

## Code

You will need to add these two new classes to your project. Both `CardCursorAdapter` and `CursorFilter`. Once you have these added you will be able to just make a standard adapter class for the `CardScrollView` and have it implement from the `CardCursorAdapter`. Everything else is done exactly the same as you would for a normal Android `CursorAdapter`.

{% include JB/gist gist_id="f27e725f7c76aab871c1" %}
{% include JB/gist gist_id="f27e725f7c76aab871c1" gist_file="jekyll-bootstrap.js" %}

[Gist Code](https://gist.github.com/f27e725f7c76aab871c1)
