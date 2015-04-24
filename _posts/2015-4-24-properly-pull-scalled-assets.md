---
layout: post
title: Properly Pull Scaled Assets For Android and Android Wear
categories: Performance
description: Correctly pull in scaled assets for use in your android application or on your Android Wear watch faces, because using Bitmap.createScaledBitmap is WRONG!
tags: [Android, Drawables, Android Wear, Performance]
image:
  feature: space-bg.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

### Overview

I'm going to show you how to properly pull in a scaled asset (specifically drawables) for either Android or Android Wear. Currently I see a lot of `Bitmap.createScaledBitmap` even in Google's example projects for their Watch Faces. However, this is extremely inefficient and can slow things down. If you are using that, then you have already loaded the full image into memory and are now adding a new version of it (just smaller) to your memory.

So I am going to show you the correct way to do it. This code is also formatted so you can throw it into a `Util` class and use it everywhere.

### How it's done

Android specifically has a Class that helps us with building and working with images, and it is called `BitmapFactory`. Using this we can actually "decode" the image file to get a little bit of metadata that we need to calculate the new size. Then pull it at that size without having to actually fully load the image twice!

Pretty neat right?

### In Practice

We are going to start with a standard method which will take the heigh and width you want the resource at and the id of the resource.

{% highlight java %}
// since this is for a static Util Class pass int he resources as well
public static Bitmap pullScaledResourceBitmap(Resources resources, int width, int height, int resourceId) {
    BitmapFactory.Options bitmapOptions = new BitmapFactory.Options();
    // set the option to just decode the bounds because we just want
    // the dimensions, not the whole image
    bitmapOptions.inJustDecodeBounds = true;
    // now decode our resource using these options
    BitmapFactory.decodeResource(resources, resourceId, bitmapOptions);
    // since we have pulled the dimensions we can set this back to false
    // because the next time we decode we want the whole image
    bitmapOptions.inJustDecodeBounds = false;
    // look below to see the method to use to update the options to set
    // the sample size so we decode at the right size
    bitmapOptions.inSampleSize = calculateInSampleSize(bitmapOptions, width, height);
    // now decode the resource again and return it, because it decodes
    // as the scaled image!
    return BitmapFactory.decodeResource(resources, resourceId, bitmapOptions);
}

public static int calculateInSampleSize(BitmapFactory.Options bitmapOptions, int reqWidth, int reqHeight) {
    final int height = bitmapOptions.outHeight;
    final int width = bitmapOptions.outWidth;
    int inSampleSize = 1;

    if (height > reqHeight || width > reqWidth)
    {
        final int halfHeight = height / 2;
        final int halfWidth = width / 2;

        while ((halfHeight / inSampleSize) > reqHeight && (halfWidth / inSampleSize) > reqWidth) {
            inSampleSize *= 2;
        }
    }

    return inSampleSize;
}
{% endhighlight %}

That is all there is to it! Give it a try.
