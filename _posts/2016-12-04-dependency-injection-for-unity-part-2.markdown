---
layout: post
title: 'Dependency injection for Unity - Part 2: What does dependency injection replace?'
date: '2016-12-04 00:51:00'
---

Have you ever struggled to keep a game or application working as it evolves and as the complexity ramps up? Software is composed of a suite of interacting components that are wired together in a particular way. As the number of interacting components grows the wiring and the number of connections between components grows exponentially (a phenomenon known as [Metcalfe's law](https://en.wikipedia.org/wiki/Metcalfe%27s_law)). 

How can we keep a handle on this complexity and carve-out some order from the chaos?

You should consider using [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection), a technique for automatically wiring together complex applications. This sounds like it might be what we need, however dependency injection can itself be complicated and difficult to understand. Try out any of the dependency injection frameworks for C# and you can be forgiven for thinking that dependency injection was some kind of rocket science. 

In this series of articles I aim to explain dependency injection in simple terms. I'll try to convince you that dependency injection will help you manage complex applications. With working examples I'll show you how to use dependency injection with Unity.

In [part 1](http://www.what-could-possibly-go-wrong.com/dependency-injection-for-unity-part-1/?utm_source=ash&utm_medium=wpcgw-di-part-2&utm_campaign=dependency-injection) I explained what dependency injection is and I argued the benefits it can provide. In this post I show the kind of code we have in Unity that dependency injection aims to replace.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
# Contents

- [What is dependency injection replacing?](#whatisdependencyinjectionreplacing)
  - [Singleton access](#singletonaccess)
  - [Drag and drop connections in the Unity Editor](#draganddropconnectionsintheunityeditor)
  - [Finding objects by name](#findingobjectsbyname)
  - [Finding objects by tag](#findingobjectsbytag)
  - [Finding objects by type](#findingobjectsbytype)
- [How do we achieve this with dependency injection?](#howdoweachievethiswithdependencyinjection)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# What is dependency injection replacing?

In the *Part 3* of this series I'm going to show you how to use dependency injection in Unity.  However, before we get to that you need to understand the kind of code we are seeking to replace with dependency injection. To that end let's look at some of the more manual methods of wiring up your application without dependency injection.  

Please don't get me wrong here, the techniques I present here are useful, they are practical and they *do* work. The problem is that manually wired connections are fragile and prone to breaking. The more connections like this you have, the more fragile your application is and the more prone it is to breaking when things change. Manual wiring doesn't scale well to large, complex and *evolving* applications. That's the big problem that dependency injection aims to solve. 

Just don't assume that you can't or shouldn't *ever* use these techniques, that's not what I am saying. Ultimately you have to make your own judgment calls as to when and where to use particular patterns and techniques. Manual *dependency wiring* can be appropriate in many situations and it can work together with dependency injection. What I am saying is that just because you use dependency injection most of the time, doesn't mean you can't use these manual *wiring* techniques part of the time, say when you have a problem that can't easily be solved by dependency injection or in performance-sensitive situations where automated dependency injection would degrade the performance. 

## Singleton access

One common way to link objects is by using [the singleton design pattern](https://en.wikipedia.org/wiki/Singleton_pattern). 

If you search the internet you will find multiple techniques on how to implement this in Unity, all are more or less equally awful. This pattern is much maligned and for good reason. It promotes bad coding techniques, such as use of global variables and [spaghetti coding](https://en.wikipedia.org/wiki/Spaghetti_code). It leads to code that's impossible to unit-test. It also creates code that is very difficult to reuse, for example you can't easily extract your *inventory system* to use in another game when it is tangled up with a dozen singletons that you *don't want* to extract.  

The thing is that singleton is **just so damn convenient**! It is an architectural pattern that makes Unity development more bearable. 

Let me explain. One of the unusual things about coding against the Unity API is that you have no *main* function. There is no centralized [entry point](https://en.wikipedia.org/wiki/Entry_point) to a Unity application. This is great for non-technical people who need a simple way to get started building games. It's difficult for coders though because you have to resort to something like the singleton pattern in order to tie together the collection of disparate scripts that comprise a Unity application.

The good news is that dependency injection can allow you to use singletons without feeling dirty and in *Part 3* of this series I'll show you how I do just that.

## Drag and drop connections in the Unity Editor

Probably the simplest way to create connections between objects is through drag and drop in the Unity Editor. This can be really convenient. As a coder this gives me the ability to create components that can then be wired up by an artist or designer. They will create connections between objects and have control over that. For example your *3rd person camera* component can be wired up to the *player* component that is in the scene.

This can be a really powerful technique: hand over control of the configuration to an artist and they will find new and interesting ways of using your code of which you wouldn't have thought. You can also leave it  to them and they wire up and rewire scenes without input from the code team. The power to change things and build new things based on existing components is very empowering for our artists and designers.

This is a double-edge sword though as you also give your artists and designers much more power to break your build through human error. In addition, if you take this to the extreme, and many do, you make it incredibly tedius to wire up the connections in a scene. Why would you want to make your designer wire up connections that could easily be made automatically? They won't thank you for wasting hours of their time. For example do you really want to manually wire up every *NPC* to the *player*? When you find that your artists are manually wiring up 100s of objects *alarm bells* should be ringing. Over time things will change, they always do, and when this happens your artists will now need to rewire those 100s of objects (sometimes multiple times). Now those alarms bells should have changed to *blaring sirens*. 

## Finding objects by name

We can find objects in the scene by code in a number of different ways. We can [traverse the scene](http://www.what-could-possibly-go-wrong.com/scene-traversal-recipes-for-unity/) or we can [query the scene](http://www.what-could-possibly-go-wrong.com/scene-query-recipes-for-unity/) to find objects by name or tag, etc. 

The simplest example is: 

	GameObject player = GameObject.Find("Player");

This code-driven approach can automate tedious manual connections. However there are problems with it. If you are a conscientious coder then will have spotted the [magic string](http://deviq.com/magic-strings/) and you might [smell something amiss](https://en.wikipedia.org/wiki/Code_smell). From a coding perspective this makes for fragile code. Especially so when we consider that it is not just code that can break our build, the hierarchy has also become something that can cause breakage, for example removing or renaming the *Player* game object *will* break this code and we won't know about it until [runtime](https://en.wikipedia.org/wiki/Run_time_(program_lifecycle_phase)).

This technique has it's uses, but it still results in questionable code. A big problem is that you have also taken control away from the artist or designer who must now conform to the convention of having a *Player* game object in the scene. We have reduced creative potential due to the limitation now placed on art/design.

You could give the designer the ability to change the name of *Player* object:

	public class NPC : MonoBehaviour 
	{
		publis string playerObjectName = "Player";

		private GameObject player; 

		void Start()
		{
			player = GameObject.Find(playerObjectName);
		}

		...
	}

Unfortunately though this just makes for more painstaking manual configuration. This may seem like a trivial case, but in practice these trivial cases accumulate and eventually result in big problems, usually manifesting right before an important deadline!

This technique would be more useful if we could find multiple objects by name. This could then be an effective way to wire up a large amount of objects. Consider the following example using the *fictional* API `FindAll` to wire up a *vehicle* to all *pedestrians*: 

	GameObject[] pedestrians = GameObject.FindAll("Pedestrian");

This is still questionable code, but at least now we have the potential to replace tedious manual wiring with something that is automated. To understand how this helps just imagine manually wiring up 10 vehicles and 10 pedestrians (that's 100 connections you just automated). 

Well we can't do this anyway... **because the `FindAll` function doesn't exist in Unity**, however we could use the [Scene Query](http://www.what-could-possibly-go-wrong.com/scene-query-recipes-for-unity/) `SelectAll` function to do this. 

## Finding objects by tag

One option that we do have for finding multiple objects is to search by [tag](https://docs.unity3d.com/Manual/Tags.html): 

 	GameObject[] pedestrians = GameObject.FindGameObjectsWithTag("Pedestrian");

This allows us to find multiple game objects at the same time, but again we have fragile and easily broken code. This technique can help make easy work of mass wiring that would be extremely tedious to do manually in the Unity Editor. The problem is the *magic string* that will stifle future refactoring and restructuring efforts and can easily break the build (say when our intrepid designer unwittingly removes the tag).

## Finding objects by type

We can make a huge improvement to the previous code example by finding games objects by type: 

	Pedestrian pedestrian = GameObject.FindObjectOfType<Pedestrian>(); 

We can also find multiple objects:

	Pedestrian[] pedestrians = GameObject.FindObjectsOfType<Pedestrian>();

This is much better as we aren't using any magic strings. An additional benefit from specifing the type exactly is that our code is now [type-safe](https://en.wikipedia.org/wiki/Type_safety). It's also much more difficult for a designer to break the build, to do so they'd have to remove the *Pedestrian* component from each pedestrian game object. Remove that component and the game object will cease to be a *pedestrian* anyway so it just works the way we want.

# How do we achieve this with dependency injection?

Dependency injection aims to automate manual connections. It gives us some piece of mind that we can restructure our scene and refactor our code and the connections, being automated, will simply rewire themselves as necessary. 

This is incredible when you think about it. I know in my own career (before I discovered dependency injection, of course) I've wasted many hours wiring and rewiring games and applications as they evolved (which they always do). When you think about it from this perspective (how much time this would have *saved me* in hindsight) you find yourself asking the question *why wasn't I already using dependency injection*?

Well the answer to that is that there is of course a learning curve to implementing and making the best use of any such pattern. Breaking through to the point where using dependency injection is a *no brainer* isn't necessarily easy to achieve and the dependency injection frameworks that are available (in my opinion) could do more to encourage their uptake. 

And that's where this series comes in. I hope to show that when viewed from the right perspective that dependency injection isn't that difficult to understand and make use of in your own game projects. Certainly there will be some complexity for you to work through, but hopefully I've convinced you that it's worthwhile for anything beyond the simplest of applications.

In *Part 3* I'll show you how to use the `Inject` attribute to construct connections between Unity game objects. The following example shows how an NPC can automatically connect to the `Player` object: 

	public class NPC : MonoBehaviour 
	{
		[Inject(InjectFrom.Anywhere)]
		public Player player; 

		void Start()
		{
			...
		}
	}

We can achieve this with multiple objects by injecting to an array property: 

	public class Vehicle : MonoBehaviour 
	{
		[Inject(InjectFrom.Anywhere)]
		public Pedestrian[] pedestrians; 

		void Start()
		{
			...
		}
	}

# Conclusion

In this article I've demonstrated the kind of code that we aim to replace with dependency injection. I've attempted to illustrate how automating these connections through dependency injection can ultimately save us time and frustration. 

Dependency injection allows our applications to evolve and be restructured without needing the kind of complex rewiring that is prone to generating disastrously broken builds.

Please stay tuned for *[Part 3](http://www.what-could-possibly-go-wrong.com/dependency-injection-for-unity-part-3/?utm_source=ash&utm_medium=wcpgw-di-article-part-2&utm_campaign=dependency-injection3)* where I finally show how to use dependency injection in Unity. 