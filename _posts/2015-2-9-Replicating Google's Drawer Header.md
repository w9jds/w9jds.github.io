---
layout: post
title: Replicating Google's Drawer Headers
categories: Walkthroughs
description: Replicating Google's Drawer Headers in Material Design.
tags: [Android, 5.0, Design, Drawer, Google, Material, Walkthrough]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

###Overview

Today I'm going to go over how to replicate the header you find at the top of the Google app drawers. If you haven't already, you can check out the previous walkthrough on how to set up your drawer in the first place. It can be found here: Create Material Design Navigation Drawer

###Dependencies

First thing you are going to need is to set up a layout that will be the header for the drawer. I personally use Piccaso to load the images. You can either use a custom transform to change the user image to a circle or you can use a circle image view. I chose the circle image view since it makes the default image circle as well. For both of these add these two dependencies to your application build.gradle:

{% highlight groovy %}
compile 'com.squareup.picasso:picasso:+'
compile 'de.hdodenhof:circleimageview:+'
{% endhighlight %}

The '+' are where the version numbers go. You can leave it this way but android studio will throw a warning about them.

###Layout Creation

Now I use the same layout for both 21 and previous so mine looks like this:

{% include JB/gist gist_id="d293dd31dea0dff38d1b" %}
{% include JB/gist gist_id="55d63527aedcdd7b425d" gist_file="jekyll-bootstrap.js" %}

###Insert Header into Drawer

The header then needs to be added into your drawer inside of your activity. This needs to be done BEFORE you attach an adapter to your listview.

Go to your activity with the drawer and find the onCreate method. I would recommend to make a private member variable for your header since we will be populating it after you have finished logging into the Google Account. We will then find and set the header for the drawer. (Now remember some of this code you probably recognize from the previous walkthrough so make sure it is in this order)

Once you add in you default profile picture, default banner picture, and an adapter (adapter must be set for the header to become visible) you can run this and you should see your header. Now all we have to do is hook up your Google account to it.

###Populating Drawer with user's Google Account

Now for the fun part. In your main activity where your drawer is going to be we need to set up a log in for the Google Api Client. On connection we will then pull the current person's name, email, banner image, and profile image. What we need to do is set up some implementations for the callbacks (failed and success) and a couple more override classes. Eventually your main activity should look something like this:

{% include JB/gist gist_id="55d63527aedcdd7b425d" %}
{% include JB/gist gist_id="55d63527aedcdd7b425d" gist_file="jekyll-bootstrap.js" %}

###Success

Now you should be able to run and see your new drawer header! Personal note I tend to like to make this the main activity and have fragments and controllers for navigation throughout everything so you only have to worry about logging in once and the drawer is always there (but that is just me).

Once you have an adapter added to this, your drawer should look something like this:

![Drawer Example Screenshot]({{ site.url }}/assets/Drawer_Screenshot.png)
