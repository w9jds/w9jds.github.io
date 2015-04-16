---
layout: post
title: Layout Change Animations (Sliding Height)
categories: Animations
description: Replicating Google's Drawer Headers in Material Design.
tags: [Android, 5.0, Google, Animations]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

### Overview

One animation I see a lot, is the slide to open. Usually this is the actual layouts of a view being changed. However, if you just change the height it will just flash open. So I will show you two ways you can actually do this animation.

### ValueAnimation

The first way you can do this, is by using a `ValueAnimation` (which can be used in an `AnimatorSet`). This animation goes though and changes the value of a set property along a parabola (or any line equation you specifically set into the Interpolator). It then goes to each point on it and then lets you create an equation based on where on that line the animation is at.

{% highlight java %}
// view we want to animate
final View messageView = view.findViewById(R.id.message_section);

// set the values we want to animate between and how long it takes
// to run
ValueAnimator slideAnimator = ValueAnimator
        .ofInt(currentHeight, newHeight)
        .setDuration(300);


// we want to manually handle how each tick is handled so add a
// listener
slideAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator animation) {
        // get the value the interpolator is at
        Integer value = (Integer) animation.getAnimatedValue();
        // I'm going to set the layout's height 1:1 to the tick
        messageView.getLayoutParams().height = value.intValue();
        // force all layouts to see which ones are affected by
        // this layouts height change
        messageView.requestLayout();
    }
});

// create a new animationset
AnimatorSet set = new AnimatorSet();
// since this is the only animation we are going to run we just use
// play
set.play(slideAnimator);
// this is how
set.setInterpolator(new AccelerateDecelerateInterpolator());
// start the animation
set.start();
{% endhighlight %}

### Custom Animation

A second more extremely solution (that doesn't seem to always work the way you want) is to create a custom class that extends `Animation`. This is much more flexible and works almost exactly the same as the above `ValueAnimator`. You can make the constructor take in the new height and old height. This also uses the interpolator so you can still set a custom equation. Here is a basic example:

{% highlight java %}
public class SlideAnimation extends Animation {

    int mFromHeight;
    int mToHeight;
    View mView;

    public SlideAnimation(View view, int fromHeight, int toHeight) {
        this.mView = view;
        this.mFromHeight = fromHeight;
        this.mToHeight = toHeight;
    }

    @Override
    protected void applyTransformation(float interpolatedTime, Transformation transformation) {
        int newHeight;

        if (mView.getHeight() != mToHeight) {
            newHeight = (int) (mFromHeight + ((mToHeight - mFromHeight) * interpolatedTime));
            mView.getLayoutParams().height = newHeight;
            mView.requestLayout();
        }
    }

    @Override
    public void initialize(int width, int height, int parentWidth, int parentHeight) {
        super.initialize(width, height, parentWidth, parentHeight);
    }

    @Override
    public boolean willChangeBounds() {
        return true;
    }
}
{% endhighlight %}

The above is almost exactly the same, but you will have to then use it somewhere, so that will look something like this:

{% highlight java %}
View messageView = view.findViewById(R.id.message_section);

Animation animation = new SlideAnimation(messageView, currentHeight, newHeight);

// this interpolator only speeds up as it keeps going
animation.setInterpolator(new AccelerateInterpolator());
animation.setDuration(300);
messageView.setAnimation(animation);
messageView.startAnimation(animation);
{% endhighlight %}

### Conclusion

Both of these will get you the same kind of result, and can be built upon. Be careful though, it doesn't always work in all places. When I made this custom animation class it doesn't work when I used it in a drawer, but `ValueAnimator`.
