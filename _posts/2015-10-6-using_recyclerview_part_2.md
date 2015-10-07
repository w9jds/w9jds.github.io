---
layout: post
title: Using RecyclerViews Part 2
categories: Walkthroughs
description: More info on RecyclerViews continued from part one
tags: [Android, RecyclerViews, Animations, Walkthrough]
image:
  feature: spaceone.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

##Overview
A couple weeks ago I talked about using `RecyclerView`s in your application and gave you a way to put `OnItemClicked` back into it. That solution only works in specific places and I want to give you another option on how to do it another way. I will also go over a couple other things, like including the ripple animation on your items. The `EnhancedRecyclerViewAdapter` I supplied in my last post showed that you can make an interface inside of your adapter so you can set and store it externally. However, this required the listener to be static so the `ViewHolder` to access it. This causes a problem if you want to use the adapter for multiple `RecyclerView`s at once (even if it is inherited from different adapter instances). So to solve this problem you need to supply the listener in the constructor, and the interface will live inside of the `ViewHolder`.

##OnItemClicked 2
Like before we want to put the `onClick` inside of our `ViewHolder`. However, instead of referencing a global set up inside the adapter, we want to use a listener that is passed to the constructor of the `ViewHolder`. All of the add methods I showed before still work for this method, so the only thing that should change is the location and declaration of the Listener. It should look something like this:

{% highlight java %}

public final class NewsAdapter extends RecyclerView.Adapter<NewsAdapter.ViewHolder> {

    private Context mContext;
    private ArrayList<Object> mItems = new ArrayList<>();
    private ViewHolder.onItemClickListener mListener;

    public NewsAdapter(Context context, ViewHolder.onItemClickListener listener) {
        mContext = context;
        mListener = listener;
    }

    public static class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
        TextView title;
        TextView subtitle;
        onItemClickListener listener;

        public ViewHolder(View itemView, onItemClickListener listener) {
            super(itemView);

            this.title = (TextView)itemView.findViewById(R.id.title);
            this.subtitle = (TextView)itemView.findViewById(R.id.subtitle);
            this.listener = listener;

            itemView.setOnClickListener(this);
        }

        @Override
        public void onClick(View view) {
            if (listener != null) {
                listener.onItemClick(view, getAdapterPosition());
            }
        }

        public interface onItemClickListener {
            void onItemClick(View view, int position);
        }
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(mContext);
        View view = inflater.inflate(R.layout.twoline_item_layout, parent, false);

        return new ViewHolder(view, mListener);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        RssItem thisItem = (RssItem) getItem(position);

        Resources resources = mContext.getResources();
        holder.title.setText(thisItem.getTitle());
        holder.subtitle.setText(String.format(resources.getString(R.string.by_format), thisItem.getAuthor()));
    }

    public Object getItem(int position) {
        return mItems.get(position);
    }

    public void clear() {
        int size = mItems.size();
        mItems.clear();
        notifyItemRangeRemoved(0, size);
    }

    public void addAllItems(Collection<?> items) {
        int startPosition = mItems.size() > 0 ? mItems.size() - 1 : 0;

        mItems.addAll(items);
        notifyItemRangeInserted(startPosition, items.size());
    }

    @Override
    public int getItemCount() {
        return mItems.size();
    }
}

{% endhighlight %}

##Ripple Animations
Okay, so you have your `RecyclerView` set up with your adapter set and full of items. However, when you tap any of the items there is no ripple like it does for the items of a `ListView`. This is actually a pretty simple fix. All you need to do is add an attribute to either the foreground, or background of your layout that is being inflated in the `onCreateViewHolder` of your adapter. The base layout element should look like this:

{% highlight xml %}

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:background="?attr/selectableItemBackground"
    android:layout_height="72dp"
    android:paddingLeft="16dp"
    android:paddingRight="16dp">

{% endhighlight %}

Notice the `?attr/selectableItemBackground` value. This is what adds the Ripple to your item.