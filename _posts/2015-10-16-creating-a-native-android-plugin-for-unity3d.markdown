---
layout: post
title: Creating a native Android plugin for Unity3d
date: '2015-10-16 01:13:03'
permalink: creating-a-native-android-plugin-for-unity3d/
disqus_id: ghost-9
---

## Introduction

This short article is a guide to creating a plugin to access the [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) API from [Unity3d](https://en.wikipedia.org/wiki/Unity_(game_engine)). This is aimed at intermediate developers. You should be comfortable using the [command line](https://en.wikipedia.org/wiki/Command-line_interface). You should be familiar with [C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)), Unity3d development and the Unity Editor. We'll gloss over some [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) code, it won't be difficult if you already know C#.
    
Recently at [Real Serious Games](http://www.realseriousgames.com/) we built an Android app using Unity3d. We needed to know the current level of the device's battery. Our app has a process that once started, at least ideally, needs to run to completion without interruption. It isn't ideal if the battery gives out part-way through. To find the current battery level and check if the device is plugged in we must query the Android API. So we created a native plugin to Unity3d. Now we can warn the user not to start the long process if the device is low on battery (and not plugged in).

This was a good opportunity for some learning and now we aim to pass on the knowledge.

## Example Project

During the article we reference the example project that is available on github:

[https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example](https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example)

You can clone it, [fork](https://en.wikipedia.org/wiki/Fork_(software_development)) it or simply download the zip file. If you plan to clone or fork we recommend using [SourceTree](https://www.sourcetreeapp.com/). 

## Getting setup for Android development

The language for Android development is Java, therefore the Android library that is our Unity plugin is coded in Java. To build the library we must install the [Java Development Kit (JDK)](https://en.wikipedia.org/wiki/Java_Development_Kit), the [Android SDK](https://en.wikipedia.org/wiki/Android_software_development#SDK) and [Apache Ant](https://en.wikipedia.org/wiki/Apache_Ant).

In this article we demonstrate installation of the stand-alone Android SDK. Recently though [Android Studio](http://developer.android.com/tools/studio/index.html) has been looking really good and you may find it easier to work with when creating this sort of project.  

Before installing the Android SDK we need to install the JDK which is a dependency and necessary for Java development. The JDK can be downloaded from [Oracle's Java downloads page](http://www.oracle.com/technetwork/java/javase/downloads/index.html). There are multiple download links on this page. You only need the *Java Platform (JDK)* which is the first download link (at the time of writing). This takes you to another page where you must accept a license agreement. Next select the download for the platform of your choice. We are using Windows so we downloaded the Windows x64 JDK download: *dk-8u60-windows-x64.exe*.

After the download has completed install the JDK. We simply used all the default settings. After the JDK is installed you can move onto setting up the Android SDK. It won't install without the JDK, which is why we installed the JDK first.

Open the web page for the [*Android stand-alone tools package*](https://developer.android.com/sdk/installing/index.html?pkg=tools). Click on the *download the SDK now* link. Then choose the latest installer for your platform. We downloaded the Windows installer: *installer_r24.3.4-windows.exe*. You can mostly get away with all the default settings when installing Android SDK Tools, however we recommend changing the installation directory from the default to something you will remember. For simplicity and for the purposes of this article we will change it to *c:\android-sdk*.

At the end of the installation you'll be prompted to start the *SDK Manager*. We need this anyway to install the Android API. You can always find SDK Manager again later by looking in the Android SDK installation directory (which we set to *c:\android-sdk*). Through the SDK Manager we must install a specific Android API version. In our testing for the Unity plugin we used *API 21*, you should choose an API version that is appropriate to the needs of your product. The minimum API version you support is something you need to consider carefully. 

Installing the API is very easy. By default the latest version will be *checked* for install. You should change ths to the API version you want. Then click *Install X packages*, accept the license agreement and the SDK Manager will download and install the API version that you selected. This will take some time.

After installing the Android SDK and required API we should check that we can access the tools. At this point you may need to restart your computer to ensure the changes from the installation take effect.

To use the Android SDK from the command line you'll need to add it's directories to your path. We can do this quickly and easily from the command line:

	set path=%path%;C:\android-sdk\tools;C:\android-sdk\platform-tools

Make sure you adjust the path depending on where you actually installed the Android SDK. The previous snippet adds the Android SDK to our path, but only in the current command line session. This is fine for immediate use, but you will want to make this [change permanent](https://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/sysdm_advancd_environmnt_addchange_variable.mspx?mfr=true) for future work.

Now you can run some commands to check the Android SDK is setup. If your paths are setup correctly the following commands will run and produce output:

	adb

To build Android projects you need to download and install [Apache Ant](http://ant.apache.org/bindownload.cgi). We are using version 1.9.6. The file to download looks something like *apache-ant-1.9.6-bin.zip*. Once downloaded unzip to a location of your choice, for this article and convenience we have unzipped to *c:\apache-ant*.

You'll need to add the *Ant bin directory* to your path:

	set path=%path%;C:\apache-ant\bin

Run the following command to check that you have Ant installed and accessible:

	ant -version

This should display the version number as 1.9.6 or whatever version you actually installed.

To build you are going to need to set the *JAVA_HOME* environment variable. Or else at some point you will probably get an error message like *Unable to locate tools.jar. Expected to find it in ...* 

You can set this from the command line as follows:

	set JAVA_HOME=C:\Program Files\Java\jdk1.8.0_60


## Creating an Android Java library 

Now you can create Android projects from the command line. You can follow along with the article either by running the example commands in this section or by studying [the example project](https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example) that accompanies this article. If you don't care for creating an Android project and just want to study the example project, please skip this section. 

With your command line open, and paths setup as previously described, change to a directory where you want to create your new project. This is going to generate a bunch of files, so it's best if you generate them in a new or clean directory.

Run the following command:

	android create lib-project --name MyProject --target 1 --path . --package com.MyCompany.MyProject

Have a look in the directory where you generated the project and you will see a bunch of new files.

We need to add at least one Java file to our library to build it. Under the *src* directory create nested sub-directories that match your *package name*. In this example the package name is *com.MyCompany.MyProject*, so we must create a *com* directory under *src*, then a *MyCompany* directory under *com* and finally a *MyProject* directory. In the *MyProject* directory create a new Java file. Let's call it *MyLibrary.java*:

	package com.MyCompany.MyProject;
	
	public class MyLibrary
	{
	    public static float GetValue()
	    {
			return 1.0f;
	    }
	} 

Before we can build the library we must add a build target to the end of our *build.xml* so that we can build the [*jar*](https://en.wikipedia.org/wiki/JAR_(file_format)) file. Your Build.xml should look like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<project name="AndroidPlugin" default="help">

		... default stuff ...

	    <target name="jar" depends="debug">
	        <jar
	            destfile="bin/MyLibrary.jar"
	            basedir="bin/classes" />
	    </target>
	
	</project>

Now you should be able to build the jar target: 

	ant jar

The build should complete very quickly. There should be a lot of output followed by a *BUILD SUCCESSFUL* message.

Now have a look in the *bin* directory and you should see that *MyLibrary.jar* has been generated. Congratulations you have built (a very simple) Android library. 

## An Android library to read the battery level

In this section we examine and build the Android library to access the battery level. If you simply want to see how to use the pre-built library from Unity3d, please skip this section.

To follow along you will need [the example project](https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example).

Once you have the example project locally you can examine the code. The example project has two main directories. The code and project for the Android library, that we will examine now, are under the *AndroidPlugin* directory. The other directory is *UnityProject* which contains the example Unity3d project that uses the Android library. 

In the previous example we showed how to create an Android library from scratch. Now we examine a slightly more substantial Android library that we can use to read the device's battery level.

Examine the source code that can be found in *AndroidPlugin/src/com/RSG/AndroidPlugin/AndroidPlugin.java*:

	package com.RSG.AndroidPlugin;

	import android.os.Environment;
	import android.os.BatteryManager;
	import android.content.Intent;
	import android.content.IntentFilter;
	import android.content.Context;

	public class AndroidPlugin
	{
		// Needed to get the battery level.
		private Context context;

		public AndroidPlugin(Context context)
		{
			this.context = context;
		}

		// Return the battery level as a float between 0 and 1
		// (1 being fully charged, 0 fulled discharged)
		public float GetBatteryPct()
		{
			Intent batteryStatus = GetBatteryStatusIntent();

			int level = batteryStatus.getIntExtra(BatteryManager.EXTRA_LEVEL, -1);
			int scale = batteryStatus.getIntExtra(BatteryManager.EXTRA_SCALE, -1);

			float batteryPct = level / (float)scale;
			return batteryPct;
		}

		// Return whether or not we're currently on charge
		public boolean IsBatteryCharging()
		{
			Intent batteryStatus = GetBatteryStatusIntent();

			int status = batteryStatus.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
			return status == BatteryManager.BATTERY_STATUS_CHARGING || 
				status == BatteryManager.BATTERY_STATUS_FULL;
		}

		private Intent GetBatteryStatusIntent()
		{
			IntentFilter ifilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
			return context.registerReceiver(null, ifilter);
		}
	} 

We have created two main public functions. *GetBatteryPct* and *IsBatteryCharging*. These are the functions that we intend to call from Unity3d C#. 

If you are setup correctly as described in the section *Getting setup for Android development* you can build the Android library and create the [*jar*](https://en.wikipedia.org/wiki/JAR_(file_format)) file: 

	ant jar

This generates the jar file *AndroidPlugin.jar* under the *bin* directory. If necessary, you can check the *date modified* to ensure that this is indeed the jar file that we just built.

## Using the Android library from Unity3d

Now we will look at the *UnityProject* sub-directory in [the example project](https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example). To follow along you will need the example project and Unity 5 installed.

The example project includes a pre-built version of the jar file described in the previous section. So you don't need to build the Android library to try this out. Although if you do build the library, or build your own from scratch you simply have to copy the *jar* over to the Unity *Assets* directory. Prior to Unity 5 you could only put plugins in a *Plugins* directory. Now you can put them anywhere in the Assets directory.

Load the *UnityProject* sub-directory as a Unity project in the Unity Editor. When you [*play*](http://docs.unity3d.com/Manual/GameView.html) this on a desktop PC you will see that the battery level is *100%*. If you [*build*](http://docs.unity3d.com/Manual/PublishingBuilds.html) and run on an Android device, you should see the current battery level for that device.

On PC you should see this:

![](/content/images/2015/10/UnityScreenshot.png)

There are two code files in the example Unity project. 

*BatteryLevelPlugin.cs* is the main code file we consider here. It is a stand-alone class that loads the Android library and calls *GetBatteryPct*:

	using UnityEngine;
	using System;
	using System.Collections;
	
	public class BatteryLevelPlugin 
	{
	    public static float GetBatteryLevel()
	    {
	        if (Application.platform == RuntimePlatform.Android)
	        {
	            using (var androidPlugin = new AndroidJavaClass("com.RSG.AndroidPlugin.AndroidPlugin"))
	            {
	                using (var javaUnityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
	                {
	                    using (var currentActivity = javaUnityPlayer.GetStatic<AndroidJavaObject>("currentActivity"))
	                    {
	                        return androidPlugin.CallStatic<float>("GetBatteryPct", currentActivity);
	                    }
	                }
	            }
	        }
	
	        return 1f;
	    }
	}

Note the if statement that checks if the code is running on the Android platform. If it is then it loads the Android library and calls our custom function. Otherwise, for example when the code is running on your desktop PC, it simply returns 1f which indicates *full battery*.

The other code file is a Unity [*MonoBehaviour*](http://docs.unity3d.com/ScriptReference/MonoBehaviour.html) that we use to render the current battery state. It makes use of [Font-Awesome](https://github.com/FortAwesome/Font-Awesome/tree/master/fonts) for the battery graphic. Check out this [cheatsheet](https://fortawesome.github.io/Font-Awesome/cheatsheet/) to see what else is on offer from FontAwesome.

## Conclusion

In this article we have shown how to create a native Android plugin for Unity3d. You have seen how we have used this plugin to allow our Unity app to talk to the Android API. Hopefully it isn't as difficult as might have thought.

## About the Authors

### Ashley Davis

Ash has been developing games professionally since 1998 with some interludes in other industries. In 2012 Ash moved into the world of serious games and simulations taking on the role of Lead Developer at Real Serious Games where he uses his experience to make game technology work for business.

Ash is both a full time developer and a contractor under the name Code Capers and is working on cloud and mobile products.

Ash regularly contributes to the open source community and is a founder and organiser of games industry meetups in Brisbane: Game Technology Brisbane and Game Development Brisbane.

[Web Page](http://www.codecapers.com.au/)  
[Linked In](https://au.linkedin.com/in/ashleydavis75)

### Rory Dungan

Rory is a an avid programmer with a keen interest in games. Currently studying a Bachelor of Games and Interactive Entertainment at QUT, he also works part time at Real Serious Games putting game technology to use for businesses in a variety of projects ranging from VR experiences to apps. In his own time Rory develops games, makes Android apps, rides a fixie and plays the cello. His favourite text editor is vim.

[Web Page](http://www.rorydungan.com/)
