---
layout: post
title: Separate your Release and Debug only code
categories: Walkthroughs
description: How to set up your project to use debug and release specific code.
tags: [Android, Animations, Walkthrough]
image:
  feature: nasa-nebulas.jpg
comments: true
share: true
---

##Overview
I'm sure you have things you want to only include inside of your project when you are making a debug build or a release build. I'm going to walk you through a way to manage this code, so you don't have to manually remove/comment it out ever time you wanted to change your build type. This walkthrough will show you how to set up your project to use [Stetho](http://facebook.github.io/stetho/) for debug builds and [Crashlytics](https://get.fabric.io/) for release builds.

##Gradle Setup
There are actually two different compile types that let you only include specific dependencies for different build types. So for Stetho on debug and Crashlytics on release, it should look like this:

{% highlight groovy %}

debugCompile 'com.facebook.stetho:stetho:1.1.1'
debugCompile 'com.facebook.stetho:stetho-okhttp:1.1.1'
debugCompile 'com.facebook.stetho:stetho-urlconnection:1.1.1'

releaseCompile('com.crashlytics.sdk.android:crashlytics:2.5.2@aar') {
    transitive = true;
}

{% endhighlight %}

##Build Folders
Inside of `*/src/` we need to create two new folders, `debug` and `release`. Inside of both of these folders, create a `java` folder. If you want to make a specific namespace this is where it would go. Since we are setting up libraries that require initialization we need to create some application classes to use. If you already have an application class, just extend these two from that one. Create these two classes in their corresponding java folders. 

{% highlight java %}

public final class DebugApplication extends MyApplication {

    @Override
    public void onCreate() {
        super.onCreate();
        Stetho.initialize(
            Stetho.newInitializerBuilder(this)
                .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
                .enableWebKitInspector(Stetho.defaultInspectorModulesProvider(this))
                .build());
    }
}

public final class ReleaseApplication extends MyApplication {

    @Override
    public void onCreate() {
        super.onCreate();
        Fabric.with(this, new Crashlytics());
    }
}

{% endhighlight %}

##Additional Android Manifests
Now all we have to do is let gradle know that we want to replace the application name property depending on the build we wanted. This requires us to create two new `AndroidManifest.xml` files. These files should sit direct childs of the `debug` and `release` folders and should look like these:

{% highlight xml %}

<manifest
    package="com.w9jds.myapplication"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        tools:replace="android:name"
        android:name=".DebugApplication"/>

</manifest>

<manifest
    package="com.w9jds.myapplication"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        tools:replace="android:name"
        android:name=".ReleaseApplication"/>

</manifest>

{% endhighlight %}

Now when you switch between builds it will automatically switch between these two files, and you don't have to worry about commenting out any code anymore!