---
layout: post
title: Using Lollipop Activity Transitions
categories: Walkthroughs
description: One thing I rarely see in applications on Lollipop are the new activity translations. Specifically, the ones that reuse elements from previous activity.
tags: [Android, RecyclerViews, Animations]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

##Overview
Now that we are starting to see Lollipop pick up a larger part of the market, we are seeing applications start to tailor their apps to these users as well. However, one thing I tend to rarely see are the new transitions that Lollipop introduced for when you are switching between activities. Specifically the transitions that happen when you are reusing elements from the previous activity in the new activity. These transitions are called `SceneTransitionAnimation`. I will be going over how to set up a pretty basic one that should be used on basically all listview activities to the content activity.

##When to use it
This is used when you are sharing UI elements between your starting activity and your finishing activity. For instance, you have a listview with all of your emails in it. For each item in the list you have the sender image to the left with the sender name and subject line. When you click that item you are going to the content of that message, so the header of that activity is going to contain the sender name, image, and mail subject. So instead of just transitioning to the next page we can have the elements from the starting activity slide into position for the contents activity. It should look something like this:

![Scene Transition Animation]({{ site.url }}/assets/open_mail_transition.gif)

##Setting up your style
Since this feature is Lollipop specific, it would stand to reason that you need to make sure to only set it up for 5.0+ devices. So we need to set up a style inside of our `styles-v21.xml` file (if you are only supporting versions 21+ then you can place this in your `styles.xml` file). In your application style we need to set up a few things so the application knows about the transition system. One of your main app styles should contain these tags:

{% highlight xml %}
    <item name="android:windowContentTransitions">true</item>
    <item name="android:windowAllowEnterTransitionOverlap">true</item>
    <item name="android:windowAllowReturnTransitionOverlap">true</item>
    <item name="android:windowSharedElementEnterTransition">@android:transition/move</item>
    <item name="android:windowSharedElementExitTransition">@android:transition/move</item>
    <item name="android:windowEnterTransition">@android:transition/slide_bottom</item>
    <item name="android:windowExitTransition">@android:transition/slide_bottom</item> 
{% endhighlight %}

Note, for this walkthrough I am setting it up `windowEnterTransition` and `windowExitTransition` to have the new activity slide up from the bottom if we are preserving some elements. You are more than welcome to change this later.

##Setting up your layout elements
So we need to let Android know what elements we want to translate to the next view. Starting in api 21 there is a new tag called `transitionName`. Now since this is api 21+, if you support lower versions you do need to make a `*-v21.xml` file for your layout. Make a string value in your `strings.xml` file so you can use it in code and multiple layouts easily. Once you have the names, we can go into the ListView item's layout and add the `transitionName` tag to the elements we want to preserve and set them to the string values we saved. It should look something like this:

{$ highlight xml %}
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:background="?attr/selectableItemBackground"
    android:layout_height="72dp"
    android:paddingLeft="16dp"
    android:paddingRight="16dp">

    <de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/item_image"
        android:transitionName="@string/transition_icon"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="center_vertical"
        android:layout_marginEnd="16dp"/>

    <LinearLayout
        android:layout_gravity="center_vertical"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:id="@+id/item_title"
            android:maxLines="1"
            android:transitionName="@string/transition_title"
            android:fontFamily="roboto regular"
            android:textColor="@android:color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="16sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/item_subtitle"
            android:maxLines="1"
            android:transitionName="@string/transition_subtitle"
            android:fontFamily="roboto regular"
            style="?android:attr/subtitleTextStyle"
            android:layout_width="wrap_content"
            android:textSize="14sp"
            android:layout_height="wrap_content" />

    </LinearLayout>

</LinearLayout>
{% endhighlight %}

Now do the same thing, but on the layout that will be used on the activity you are transitioning to. Use the same string value on the item that will show the same exact content as the previous page. So the header of my mail message page looks something like this:

{$ highlight xml %}
<LinearLayout
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@drawable/bottom_border"
    android:padding="16dp">

    <TextView
        android:id="@+id/message_title"
        android:textSize="16sp"
        android:transitionName="@string/transition_subtitle"
        android:textColor="@android:color/black"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>

<LinearLayout
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/sender_image"
        android:transitionName="@string/transition_icon"
        android:layout_marginStart="16dp"
        android:layout_marginTop="16dp"
        android:layout_marginEnd="16dp"
        android:layout_width="43dp"
        android:layout_height="43dp" />

    <LinearLayout
        android:layout_marginTop="16dp"
        android:layout_marginBottom="16dp"
        android:layout_marginEnd="16dp"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:id="@+id/sender_name"
            android:transitionName="@string/transition_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="3dp"
            android:fontFamily="roboto regular"
            android:textColor="@android:color/black"
            android:textStyle="bold"
            android:textSize="16sp"/>
{% endhighlight %}

##Starting the new activity
Alright, so you have all the layout changes you had to make to put this transition in place. Now all you have to do is properly create the intent to start your new activity. Here we need to add a special option bunle to the application compat start activity call. It is pretty simple too. What you need to is to make sure to pull in `android.support.v4.util.Pair` into the class you are going to make the `startActivity` call from. With that you will then build your intent and call like this:

{$ highlight java %}
Intent intent = new Intent(getActivity(), MessageActivity.class);
intent.putExtra(Constants.CURRENT_MESSAGE, header.getMessageId());

ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(
        getActivity(),
        new Pair<View, String>(view.findViewById(R.id.item_image),
                getString(R.string.transition_icon)),
        new Pair<View, String>(view.findViewById(R.id.item_title),
                getString(R.string.transition_title)),
        new Pair<View, String>(view.findViewById(R.id.item_subtitle),
                getString(R.string.transition_subtitle))
);

ActivityCompat.startActivity(getActivity(), intent, options.toBundle());
{% endhighlight %}

All you have to do is add in a new pair for each item you want to use the transition on. So use `findViewById` for each element and then pass in the resolved string you used to it's `transitionName`. That is how simple it is. Almost no effort to add a nice fluid transition between your activities for you users. (Note, I have `getActivity()` above because I make the call from a fragment so make sure to replace those accordingly based on where you are building this).