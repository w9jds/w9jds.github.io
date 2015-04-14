---
layout: post
title: Replicating Android TV's Home Screen ImageCardView Info Expand
categories: Walkthroughs
description: How to make LeanBack's ImageCardView expand when the user focuses on it. Much like the cards on the home screen.
tags: [Android TV, Leanback, Design, Google]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

### Overview

When you use the Android TV you might notice that when you focus on a card, the title area expands so you can read everything. However, if you use the ImageCardView this functionality does come baked in. So I will show you how to set up the `OnFocusChangeListener` to mimic this functionality.

### Gist with Required Code

{% include JB/gist gist_id="22d8652bfaebfeb83407" %}
{% include JB/gist gist_id="22d8652bfaebfeb83407" gist_file="jekyll-bootstrap.js" %}

Feel free to adjust the amount of lines to display. This utilizes using a wrap content layout so it automatically expands when a layout parameter changes. If you really want to go a step farther and make it fancier I'm sure you could use a `ValueAnimator` to use an Interpolator to slide it open and have it use `requestLayout()`. If you do go that route you will need to render the content manually to get the bounds so you know how much to slide open.

### Example in action

![Title Expand]({{ site.url }}/assets/palette_expanding_info_panel.png)
