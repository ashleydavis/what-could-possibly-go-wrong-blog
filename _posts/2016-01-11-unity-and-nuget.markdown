---
layout: post
title: Unity and NuGet + JSON.net
date: '2016-01-11 09:05:32'
permalink: unity-and-nuget
disqus_id: ghost-13
---

Are you Unity developer wondering what NuGet is? 

Read on to learn about NuGet and how to use it with Unity.

# Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Introduction](#introduction)
- [On reinventing the wheel](#onreinventingthewheel)
- [Problems with Unity and NuGet](#problemswithunityandnuget)
- [Using NuGet with a normal Unity project](#usingnugetwithanormalunityproject)
- [Using NuGet with a separate Visual Studio solution](#usingnugetwithaseparatevisualstudiosolution)
- [Case Study: Installing and using JSON.Net](#casestudyinstallingandusingjsonnet)
  - [Preparation](#preparation)
  - [Installing the package](#installingthepackage)
  - [Testing the package](#testingthepackage)
- [Alternatives](#alternatives)
- [Conclusion](#conclusion)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Introduction

After talking about [Unity and DLLs](http://www.what-could-possibly-go-wrong.com/unity-and-dlls/) it seems natural to to now talk about *Unity and [NuGet](https://en.wikipedia.org/wiki/NuGet)*. 

NuGet is a [package-manager](https://en.wikipedia.org/wiki/Package_manager) for installation and management of [.NET Framework](https://en.wikipedia.org/wiki/.NET_Framework) packages. In the .NET world NuGet is used to manage the dependencies for [Visual Studio solutions and projects](http://www.what-could-possibly-go-wrong.com/unity-and-visual-studio/).

NuGet can be used from the [command line](https://docs.nuget.org/consume/command-line-reference) or from [Visual Studio](https://en.wikipedia.org/wiki/Microsoft_Visual_Studio) which has built-in support for it. In Visual Studio we can use the [package manager console](https://docs.nuget.org/consume/package-manager-console) or the package manager GUI (demonstrated in the walkthrough below).

When working with Unity we can use NuGet to install DLLs into our projects. This gives us convenient access to a new world of .NET libraries. We'll need to work-around various issues to achieve this. Whilst NuGet works seamlessly with normal Visual Studio solutions, to use with Unity we must address several issues, such as Unity's tendency to regenerate the solution (which by default would wipe out our installed packages).

The problems that we must work though to use NuGet are disparaging. But push on. In this article I'll show you how to configure NuGet and Unity to install packages and I'll demonstrate with a case study of installing the very useful [Unity version of JSON.net](https://www.nuget.org/packages/Unity.Newtonsoft.Json/).

For this article I am working with *Unity 5.2.3p2 (64 bit)* and *Visual Studio 2015 Update 1*.

# On reinventing the wheel

Package-managers offer a simple way to install the dependencies (and dependencies of dependencies) we rely on for coding. We use something like NuGet because we can move faster and achieve more when we build on the work of others. You should prefer not to [reinvent the wheel](https://en.wikipedia.org/wiki/Reinventing_the_wheel): when a package exists that meets your need and you are able to use it, then you should use it! There is little point to recreating work that has already been done.
 
NuGet is one way of reusing code. Of course using code written by others is rarely a cakewalk and there often *are* valid reasons to reinvent the wheel. The most pertinent is when the package you want doesn't work in Unity or doesn't have the performance required for a real-time game. There are other reasons you may want to rewrite code. You may want the learning experience or you may believe that you can build a better version and that the time investment required is worthwhile. 

Most of the time, when you can, you should simply reuse existing work. Don't spend your time rewriting code. *Spend your time* making a great game. By the same token.... you should [time-box](https://en.wikipedia.org/wiki/Timeboxing) your efforts to integrate existing packages and code, if it *doesn't work out* for you in a certain amount of time you should consider abandoning that tech and try something else, such as coding it yourself.

There are rarely easy answers to questions like this, use the best judgment you can muster in the moment, think critically and don't be afraid to cut your losses and change direction when things aren't working out.    

# Problems with Unity and NuGet

Using Nuget with Unity is possible and has benefits, but you need to put in some work to get there. NuGet works really well with Visual Studio and is invaluable for normal .NET programming, if you have used it for that previously then you know this already. However when working with Unity it throws some spanners in the works for us. 

Unity generates the Visual Studio solution for us. This will wipe out any NuGet packages you may have installed. This is easy to workaround though with a custom NuGet configuration file. 

More of a problem is that the default solution generated by Unity won't allow installation of most NuGet packages. Even when you change the *target framework* manually many NuGet packages won't work for you. This is because those packages just won't work on the old version of Mono that Unity is based on. 
 
# Using NuGet with a normal Unity project

The difficulties of using NuGet with Unity boil down to the following issues:

- Unity is built on an old version of Mono. So we are limited in what we can use from NuGet.
- Unity sets our *Target Framework* by default to something that will prevent most NuGet packages from being installed.
- Unity regenerates its Visual Studio solution, which wipes out installed packages.
- Unity only recognizes DLLs placed under the *Assets* folder, by default NuGet won't install packages where we want them. 

Below I'll examine how to work-around these issues to successfully use NuGet with Unity.

# Using NuGet with a separate Visual Studio solution

Using NuGet with a separate Visual Studio solution is much easier than using NuGet with a solution that has been generated by Unity. 

In my [previous article](http://www.what-could-possibly-go-wrong.com/unity-and-dlls/) I talked about the benefits of maintaining a separate solution. I discussed the benefits (and pitfalls) of building DLLs manually (normally Unity builds them for you) and copying the pre-built DLLs over to the Unity project. If you are working with Unity in this way (which isn't without its costs, be aware) then you are well situated to use NuGet. Many of the drawbacks I have mentioned don't apply to you if this is the case. You still have to be find the right packages, whichever way you work you must find packages that work with Mono/Unity and have the right target framework (.NET 3.5).

As mentioned in the previous article I recommend a balanced approach. Use a combination of Unity generated solution and a separate solution. In the separate solution you will have fewer NuGet related issues and restrictions.  
    
# Case Study: Installing and using JSON.Net

This section will walk you through installing JSON.Net from NuGet. Note that you can only install the special Unity version [*Unity.Newtonsoft.Json*](https://www.nuget.org/packages/Unity.Newtonsoft.Json/). This version was forked from the [original](https://github.com/JamesNK/Newtonsoft.Json) and modified to work with Unity. 

Please note that while this version of JSON.Net has been tested on PC and Android, I have been told that it doesn't work on iOS. If someone wants to get it working on iOS please contribute to [the GitHub repo](https://github.com/codecapers/Unity.Newtonsoft.Json).

## Preparation   

To follow along you'll first need to create a Unity project, then create a C# script and attach that script to a game object in the *hierarchy*. I covered this in [a recent article](http://www.what-could-possibly-go-wrong.com/unity-and-visual-studio/), please take a look at that if you need more help on getting setup.

Open the Visual Studio solution from Unity and make sure that you can compile. Before making any change or doing any experiment you should always try compiling first. You don't want to start with broken build, otherwise how would you know if you break it with new changes? You have only just created a new project, but it's always useful to check otherwise you don't know.

In your *Solution Explorer* you should see something like this:

![](/content/images/2016/01/Screenshot_1.png)

### .NET 2.0 Subset

The first thing we must do is change Unity's *Api Compatibility Level*. Normally it is set to *.NET 2.0 Subset*, but this limits the DLLs that we can install from NuGet.

Go to [*Player Settings*](http://docs.unity3d.com/Manual/class-PlayerSettings.html) and change it so it looks like this:

![](/content/images/2016/01/Screenshot_9.png)

There isn't a whole lot about this setting in the Unity documentation, although I did find some information on this at the end of the page on [*reducing build size*](http://docs.unity3d.com/Manual/ReducingFilesize.html). You can also find more about this by searching for [*unity .net 2.0 subset*](https://www.google.com.au/search?q=unity%20.net%202.0%20subset).

If you don't change to *.NET 2.0* you'll get the following rather senseless exception when you try to *Play* or *build* with a DLL that doesn't conform: 

		Unhandled Exception: System.Reflection.ReflectionTypeLoadException: The classes in the module cannot be loaded.
		  at (wrapper managed-to-native) System.Reflection.Assembly:GetTypes (bool)
		  at System.Reflection.Assembly.GetTypes () [0x00000] in <filename unknown>:0	
		  ... 


### Target Framework

We also need to make a change to our solution. For each project that you want to be able to install NuGet packages into you must change the *Target Framework* from *Unity 3.5 .net ... Base Class Libraries* to *.NET Framework 3.5*. This will allow you to install .NET 3.5 packages from NuGet (although this doesn't necessarily mean they will work with Mono/Unity). 

You project setting should look something like this:

![](/content/images/2016/01/Screenshot_6a.png)

Note that when Unity regenerates your solution the Target Framework will be reset to its original value. This shouldn't affect you after your NuGet packages are installed. However if you need install or upgrade NuGet packages you will need to change it again to *.NET Framework 3.5*.

If you try to install a NuGet package into your solution without having changed Target Framework you will most likely get an error like this: 

	Could not install package *<X>*. You are trying to install this package into a project that targets *<Y>*, 
	but the package does not contain any assembly references or content files that are compatible with that
	framework. For more information, contact the package author. 

After changing Target Framework you must check the Solution Explorer, look for the *yellow triangle icons* and remove any broken references. I found that *Boo.Lang* and *UnityScript.Lang* were both broken. I don't use [Boo](http://spin.atomicobject.com/2012/12/29/game-programming-boo-unity-engine-part-1/) or [UnityScript](http://wiki.unity3d.com/index.php/UnityScript_versus_JavaScript) so I was happy to just remove both.

![](/content/images/2016/01/Screenshot_10.png)

### Nuget config file

To install NuGet packages into Unity and have them survive Unity's regeneration of the solution, you must create a custom NuGet configuration file that instructs NuGet to store it's packages under Unity's *Assets* folder instead of under a *packages* folder next to the solution (which isn't in the Assets folder). Our DLLs must live under the Assets folder so that Unity can find them and including them in the solution when it is regenerated.

Create a file called *nuget.config* next to the solution for your project (the *sln* file). The root level of your Unity project will end up looking like this:

![](/content/images/2016/01/Screenshot_11.png)

Note the *packages.config* file. NuGet generates this file (it won't exist until you install your first package), it contains a list of packages you have installed in the project.

The only setting we need in *nuget.config* is `repositoryPath`. We use this to tell NuGet to install packages under the Assets directory. Your nuget.config should look like this:

	<?xml version="1.0" encoding="utf-8"?>
	<configuration>
	    <config>
		    <add key="repositoryPath" value=".\Assets\packages" />
		</config>
	</configuration>

After creating the NuGet config file you should restart Visual Studio to make sure the config changes take effect.

Most of what I learnt about NuGet configuration was from [this StackOverflow post](http://stackoverflow.com/questions/4092759/is-it-possible-to-change-the-location-of-packages-for-nuget/4197201#4197201) and from [the NuGet docs](https://docs.nuget.org/consume/nuget-config-file). 

## Installing the package

If you have gone through the preparation in the previous section you should now be ready to install your first package.

With Visual Studio open, go to the Solution Explorer, right-click on the solution (not the project). 

Select *Manage NuGet Packages for Solution...*. It's convenient to manage packages for the entire solution rather then individual projects.

![](/content/images/2016/01/Screenshot_2.png)

You are now in the NuGet Package Manager. Search for *Unity Json* and you should see *Unity.Newtonsoft.Json*:

![](/content/images/2016/01/Screenshot_4.png)

Select the package, then in the right-hand pane *check* the projects for which you want to install the package. Your project may already be checked by default. Click *Install* to download and install the package.

![](/content/images/2016/01/Screenshot_5.png)

The first time you install a package you are prompted to confirm. Select *Do not show this again* so you don't have to go through this confirmation again.

If the package has installed successfully you should now see it is a reference:

![](/content/images/2016/01/Screenshot_12.png)

## Testing the package

You've installed the package, now you need to test that you can use it. You should write some simple code that uses some of the classes and methods from the package. This will verify that the package has been installed correctly and that you can use it under Unity.

For example, here is a complete script that tests that JSON.net is installed and usable:

	using UnityEngine;
	using System.Collections;
	using Newtonsoft.Json;
	
	public class SerializationTest
	{
	    public int x = 1;
	    public string s = "foo";
	
	    public override string ToString()
	    {
	        return "x: " + x + ", s: " + s;
	    }
	}
	
	public class NewBehaviourScript : MonoBehaviour 
	{	
		void Start () 
		{	
	        var serialized = JsonConvert.SerializeObject(new SerializationTest() { x = 5, s = "blah" });
	        Debug.Log(serialized);
	
	        var deserialized = JsonConvert.DeserializeObject<SerializationTest>(jsonString);
	        Debug.Log(deserialized);	
		}
	}

This script generates the following output:

	{"x":5,"s":"blah"}
	UnityEngine.Debug:Log(Object)
	NewBehaviourScript:Start() (at Assets/NewBehaviourScript.cs:22)

	x: 5, s: blah
	UnityEngine.Debug:Log(Object)
	NewBehaviourScript:Start() (at Assets/NewBehaviourScript.cs:25)


You'll notice that I implemented `ToString` for the `SerializationTest` class. This allows me to print out the deserialized object to verify that it conforms to my expectations.


# Alternatives

There is an alternative to NuGet called [*Paket*](https://fsprojects.github.io/Paket/). I haven't tried it yet but it looks promising and may work better with Unity. If you have experience with this please email and let me know what you think of it.

If you are already using NuGet and want to try Paket, here is a [doc for converting](https://fsprojects.github.io/Paket/convert-from-nuget-tutorial.html).

# Conclusion

This article has covered using NuGet with Unity. NuGet is popular and very useful in the .NET world, but is tricky to get working with Unity. 

NuGet allows us to install useful DLLs. Unfortunately many don't work out of the box with Unity, that can be frustrating. However those DLLs that do work can be incredibly useful and will save you much time. That's time that you can spend working on you game play rather than writing code. Those that don't work can be modified to work (presuming they are open-source), but you will need time and patience and please make sure you share your work with the rest of us! 

I have addressed a number of issues that get in our way when using NuGet with Unity. The main issue is that most packages don't work with Unity. This is simply due to Unity's badly outdated version of Mono. If Unity supported .NET 4.5 we'd have so many more packages that would just work for us.

# Resources

- [https://www.nuget.org/](https://www.nuget.org/)
- [http://docs.nuget.org/](http://docs.nuget.org/)
