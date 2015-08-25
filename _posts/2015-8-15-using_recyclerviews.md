---
layout: post
title: Using RecyclerViews
categories: Walkthroughs
description: Using RecyclerViews in your application. I will go over how to fix the removal of onItemClickListner and how to properly take advantage of the build in item animations.
tags: [Android, RecyclerViews, Animations]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

##Overview
`RecyclerView` is meant to replace the `ListView` since it is built to have better performance handling your recycling and remaking of views. There are a lot of advantages to using them, like a class already made for you to use as a ViewHolder. However, there are some things that are missing from the `RecyclerView` like the `onItemClickedListener`. I'm going to show you how to add that functionality back into your `RecyclerView` along with adding and removing items correctly and letting the adapter know how to animate the changes.

##Fix Missing ItemClickedListener
First thing you will probably notice when you are going to implement your listeners on your new `RecyclerView` is that the `setOnItemClickedListener` method is gone. Don't panic, this is actually pretty easy to add back in. Go inside of your adapter you have set up for your `RecyclerView` and add in an interface like so:

{% highlight java %}

public interface onItemClickListener {
    void onItemClick(int position);
}

{% endhighlight %}

Along with that you will want to add in a static variable to the top of your adapter to store the listener you will be storing, so it should be something like this:

{% highlight java %}

static onItemClickListener mItemClickListener;

{% endhighlight %}

Cool, so now we have the listener, but how do we add it? Well we just add in a public method that does the same thing that the old one did. Add `setOnItemClickListener`:

{% highlight java %}

public void setOnItemClickListener(final onItemClickListener itemClickListener) {
    mItemClickListener = itemClickListener;
}

{% endhighlight %}

Now we are talking, just one more part you need to implement. We need to somehow trigger this event when we click on one of the items. We just need to add that inside of our ViewHolder. So it should look something like this:

{% highlight java %}

public static class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {

    //Your UI elements here

    public ViewHolder(View itemView) {
        super(itemView);

        //findViewById to populate your elements here

        itemView.setOnClickListener(this);
    }


    @Override
    public void onClick(View v) {
        if (mItemClickListener != null) {
            mItemClickListener.onItemClick(getAdapterPosition());
        }
    }
}

{% endhighlight %}

This will get you the same functionality as the old `onItemClick` event.

## Adding and Removing Items

So one of the nice features of a `RecyclerView` is that when you add and remove items the `ItemAnimator` you set auto animates the movement of you items. However, you need to tell it HOW to handle the data changes to properly trigger these animations. There are no built in add or remove methods so I will go over some basics on how to do this.

In practice using `notifyDataSetChanged` should only be used as a last resort since this will just refresh all of your items. So what we want to do is set up a could methods inside of your adapter that will allow you to add items, but only notify the items that have changed or moved so that the Recycler can properly animate them. Here are the two methods for adding one item:

{% highlight java %}

public void addItem(Object item, int position) {
    mItems.add(position, item);
    notifyItemInserted(position);
    notifyItemMoved(position, mItems.size() - 1);
}

public void addItem(Object item) {
    int position = mItems.size() > 0 ? mItems.size() - 1 : 0;

    mItems.add(item);
    notifyItemInserted(position);
}

{% endhighlight %}

Notice how I place the item inside of the list of items, but instead of telling it I changed the dataset, I just say I pushed the item to a specified location and let the items after that, that they have moved. Not the same will go for adding in a range of items:

{% highlight java %}

public void addAllItems(Collection<?> items) {
    int startPosition = mItems.size() > 0 ? mItems.size() - 1 : 0;

    mItems.addAll(items);
    notifyItemRangeInserted(startPosition, items.size());
}

public void addAllItems(Collection<?> items, int position) {
    mItems.addAll(position, items);
    notifyItemRangeInserted(position, items.size());
    notifyItemMoved(position, mItems.size() - 1);
}

{% endhighlight %}

Great! So we can now add in both a single item, and a range of items, but what if we want to remove items? Well that is almost exactly the same, just instead of insert we remove the item. It should look something like this:

{% highlight java %}

public void removeItem(int position) {
    mItems.remove(position);
    notifyItemRemoved(position);
    notifyItemMoved(position, mItems.size() - 1);
}

{% endhighlight %}

Alright, just one more left. Say we want to completely clear out the `RecyclerView`. Well just add in a `clear` method to handle that for you:

{% highlight java %}

public void clear() {
    int size = mItems.size();
    mItems.clear();
    notifyItemRangeRemoved(0, size);
}
    
{% endhighlight %}

With that you are all set! 

##Bonus

I did end up making an `EnhancedRecyclerViewAdapter` that you can throw into your appliation so that all you do is make an adapter and it handles everything for you if you extend it. It can be found here:

https://gist.github.com/w9jds/67a020b2c27a91c434c8

I will be creating this along with a couple more controls into a library for use through maven in the future, but until then you can just include that into your project.