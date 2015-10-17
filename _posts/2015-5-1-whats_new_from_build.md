---
layout: post
title: What's New From Microsoft's //Build/ 2015 (includes code)
description: Here is a roundup of some of the new things that came out of build the last few days.
tags: [.Net, XAML, Microsoft, Visual Studio, C#, Hololens, Windows 10]
image:
  feature: space-bg.jpg
  credit: CCP
  creditlink: EveOnline.com
comments: true
share: true
---

### Overview
Starting on the 29th Microsoft kicked off //Build/ 2015. For developers that watched at home and attended the event, we expected huge news about Hololens and Windows 10.

What we got? So much more.

### Windows Phone
Lets start with what new things we have for Windows Phone. The biggest announcement was that Microsoft will now support you bring over Objective C (from your iOS apps) and Java (from your Android apps). This means that if you have an iOS or Android app you can bring it over to Windows Phone while changing very little amount of code. The best example they gave was Candy Crush by King. That is an iOS app brought over to Windows Phone. 

For iOS you can literally pull the iOS project into Visual Studio, and with a few tweaks should be able to get it fully running without a huge refactoring. 

For Android you apparently don't even have to bring it into Visual Studio. You are supposed to be able to just build it for Windows Phone from Android Studio (though I didn't see anything more about it).

Finally we got to see a deep dive into what to expect from Windows 10 on the phone. One of the coolest things about this is Continuum. This allows you to plug your phone into a monitor and have a fully functional computer at your disposal. With Universal Apps, this allows the App to fully scale to the new screen without issue and work just like it would on a standard desktop. This greatly changes the game because in some places the only smart devices people have are phones.

### Hololens
When Microsoft showed off Hololens at the beginning of the year, a lot of people where skeptical. Their first reactions where somewhere along the lines of "Yeah, okay. That video is totally fake." However, when Hololens entered the stage this week, it blew everyone away. Not only were they able to deliver on what they had originally showed off. They went even farther. Guests from different fields took stage to show off what they have been able to accomplish with this new device. We had Makers, Doctor, and Architects, and that is only the beginning.

After that they went on to tell us about development for Hololens. They showed us demos of how we can bring over applications that we have made for other platforms like it was nothing. Universal Applications have the ability to make things for Windows Phone, TV, Xbox, Desktop, Tablets, and Hololens all with one project. 

They even went one last step. They brought a couple hundred of them to //Build/ to let developers both try them, and create an application for them.

### Visual Studio Code
The first thing that Microsoft pushed out was something called Visual Studio Code. This is a light weight editor for use on Mac, Linux, and Windows.

Yes, you heard that right.

This editor is built on the same platform that other popular editors are built on top of. Like Github's Atom editor. What sets it apart though is that they brought some of the power from Visual Studio with it. Intellisense is extremely good, and you can deploy and debug code directly from the editor. The supported deployable code bases at the moment are ASP.Net and Node.js but they plan on adding much more. We even get Gulp tasks, Git integration, and more! 

You also read that correctly as well. Microsoft has released an editor with Visual Studio's Roslyn built into it for both Mac and Linux. This is just a stepping stone to their greater vision of allowing any kind of development on all platforms.

Check it out here: [Get Visual Studio Code!](http://code.visualstudio.com)

### Visual Studio
Microsoft also showed off a new Visual Studio and Blend as well. Coming with Visual Studio 2015 is their new open source compiler Roslyn, along with a whole new IDE. For people that have been using Visual Studio for years, it doesn't look to new, but they built it from the ground up and added LOADS of new features. Unfortunately I can't cover all of them, but I can hit some of the big ones.

First is the debugger. You are now able to edit code on the fly while you are debugging. No more of that purple line that makes you recompile and run if you change something. It even supports writing something then backing it up and running it again. Crazy right?! It gets better. You can also now use full lambda expressions in both the immediates window, and in your watch list.

They moved on to then show off some of the new renaming systems. When you do a rename you no longer have to deal with that annoying pop-up window, you can just do it inline without having to leave your keyboard. They even went a step farther to automatically look for conflicts and try to help you solve them. Renaming a new variable to the same name of a property in your object? Oh, that is easy to fix we can just add a `this.` in front of the old use of that name.

That brings me to error handling. There where a huge amount of error handling changes that they showed off, but in general `ctrl + .` is your best friend. It can do way more for you than it ever could.

Another big announcement was from the game section. Visual Studio now has an offical extension that allows you to create Minecraft Mods! Yup, Minecraft Mods. Instead of having to use that dreadfully awful Eclipse you can use these custom project types with amazing intellisense and some sdks to help you get started on your very first or most advanced mod! On top of that you can even debug and run it directly from Visual Studio. How cool is that?! You can find the extension here: [Get Minecraft Extension!](https://visualstudiogallery.msdn.microsoft.com/043ab247-8771-4bb1-92af-d76e542724ad)

We also finally have an offical Github Extension directly built into Visual Studio! This is part of the Team explorer and bring full support for handling, cloning, and working with all of you github repos.

Finally, They announced that they are partnering with Docker to bring offical Docker support to Visual Studio. You can now install the extension and you can build and publish ASP.Net v5 web or console apps directly to a container running on a Linux VM. You can find this extension here: [Get Docker Extension](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

### C# and XAML
With .Net 6 they aren't going to be changing so much that it will cause us to relearn things all over again. Such a relief right? They even made some quality of life changes to help make our lives easier. Keep in mind these are just a few of the big ones, there are so many more that I won't cover.

One huge one is that they are working on bring XAML debugging and binding debugging either in this release or the next one.

They made some nice changes to properties for us. Usually when you had a readonly property you usually had to do something like this:

{% highlight c# %}
private readonly string _x;

public string X
{
    get
    {
        return _x;
    }
}

public example(string x, string y)
{
    _x = x;
    _y = y;
}
{% endhighlight %}

However, they have now simplified it down to this:

{% highlight c# %}

private readonly string _x;

public string X => _x;

public Api(string x, string y)
{
    _x = x;
}

{% endhighlight %}

Next they showed off a new way to use `string.Format`. Usually a string format would look something like this:

{% highlight c# %}

KeyValuePair<string, string> x = new KeyValuePair<string, string>();

string query = string.Format("{0}={1}", x.Key, x.Value);

{% endhighlight %}

but now all you have to do is this:

{% highlight c# %}

KeyValuePair<string, string> x = new KeyValuePair<string, string>();

string query = $"{x.Key}={x.Value}";

{% endhighlight %}

How about an object that has an attributes property that you want to initialize, usually you need to add each one one at a time. Like so:

{% highlight c# %}

Dictionary<string, object> keys = new Dictionary<string, object>();
keys["id"] = Guid.Empty;
keys["name"] = "Jeremy";

{% endhighlight %}

Now you are able to initialize values inline like properties on a normal object:

{% highlight c# %}

Dictionary<string, object> keys = new Dictionary<string, object>()
{
    ["id"] = Guid.Empty,
    ["name"] = "Jeremy"
} ;

{% endhighlight %}

Another huge one is when you have a nullable object and you have an if statement where you want to check if it is null. Usually you would have something like this:

{% highlight c# %}

Dictionary<string, object> keys = new Dictionary<string, object>()
{
    ["id"] = Guid.Empty,
    ["name"] = "Jeremy"
};

if (keys != null && (Guid)keys["id"] == Guid.Empty)
{
    // Do something
}

{% endhighlight %}

You can now greatly simplify if statements by doing something like this:

{% highlight c# %}

Dictionary<string, object> keys = new Dictionary<string, object>()
{
    ["id"] = Guid.Empty,
    ["name"] = "Jeremy"
};

if ((Guid)keys?["id"] == Guid.Empty)
{
    // Do something
}

{% endhighlight %}

Finally the last one I'm going to go over is for async methods. Right now you can't really use an await inside of a `case` or `finally`. However, starting now you can actually do just that! No more of that disgusting workarounds that you had to come up with to get around it. They even added the ability to append the `case` with a `when` after it to make it more specific. Like so:

{% highlight c# %}

try
{

}
catch (Exception ex) when (ex.Data == null)
{

}
finally
{

}
    
{% endhighlight %}

This only scratches the surface of everything that was shown at //Build/ but hopefully this gives you a head start. 

Happy Coding.