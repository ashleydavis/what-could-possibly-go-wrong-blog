---
layout: post
title: Scene query recipes for Unity
date: '2016-09-30 01:17:00'
permalink: scene-query-recipes-for-unity/
disqus_id: ghost-22
---

There are times when you need to search the Unity hierarchy for particular game objects. Often you need to find them by name, tag or component type. I have found that on occasion I've wanted to wanted a more expressive and flexible way of finding game objects. Some years ago when I was attempting to improve my skills in [CSS](https://en.wikipedia.org/wiki/Cascading_Style_Sheets) hackery I made an important connection. 

I thought: what if I could query a Unity hierarchy using an expressive query language that is like [CSS selectors](https://en.wikipedia.org/wiki/Cascading_Style_Sheets#Selector)? 

Or put another way: what if I could query the hierarchy as if it was some sort of database? 

It was thoughts like this that lead to creation of the [*Scene Query* library](https://github.com/Real-Serious-Games/Unity-Scene-Query).  

In this article I show various examples of finding game objects in the hierarchy using the native Unity UI. Then I'll show you how to level up and write advanced queries to pattern match and find specific objects in the scene.  

If you want to skip the fluff and checkout the advanced features please jump directly to the [Advanced Features](#advancedfeatures) section, you can always read the rest of the article later.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [My setup](#mysetup)
- [Example code](#examplecode)
- [Finding game objects using the Unity API](#findinggameobjectsusingtheunityapi)
- [What can't we do with the Unity API?](#whatcantwedowiththeUnityAPI)
- [Finding game objects with the Scene Query API](#findinggameobjectswiththescenequeryapi)
- [Performance considerations](#performanceconsiderations)
- [Conclusion](#conclusion)

# My setup

I am currently using Unity 5.3.4 and Visual Studio Community 2015.

# Example code

An example Unity project demonstrating all these techniques [can be found on github](https://github.com/Real-Serious-Games/Unity-Scene-Query-Examples). Please download this to follow along with the examples.

The core scene query code can be found at [its own github repository](https://github.com/Real-Serious-Games/Unity-Scene-Query). 

These examples are trivial and rather contrived, however they are derived from real world examples. You'll see in a number of places I use examples such as *finding the player object* and *connecting a vehicle to multiple pedestrians* (possibly so that its AI can avoid them). 

Note that all numbered examples below have a working example in the example Unity project.

# Finding game objects using the Unity API 

Before we get into the scene query language, let's look how to search for game objects using the native Unity API.

## 1. Find object by name

This is how to find a single object by name:

	var player = GameObject.Find("Player");
	Debug.Log("Found game object " + player.name);

Note that the *Player* object can be anywhere in the hierarchy.

## 2. Find objects by name

Ok, so Unity doesn't actually allow you to find multiple objects by name. If it did you might be able to call a function like `FindObjects`:

	var pedestrians = FindObjects("Pedestrian")
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found game object " + pedestrian.name);
	}

Of course that function isn't a part of the Unity API. But you can implement it yourself easily (with some help from [LINQ Where](http://www.dotnetperls.com/where)):

	public IEnumerable<GameObject> FindObjects(string name)
	{
		return GameObject.FindObjectsOfType<GameObject>()
			.Cast<GameObject>()
			.Where(gameObject => gameObject.name == "Pedestrian")
			.ToArray();
	} 

## 3. Find direct child object by name

You can easily find the direct child of a particular parent game object by calling the `FindChild` method of `Transform`:

	var child = parent.transform.FindChild("Child");
	Debug.Log("Found child object: " + child.name);

## 4. Find objects by tag

If you need to find a collection of game objects based on name your best option in the Unity API is to find by tag. For this to work you need to [tag](https://docs.unity3d.com/Manual/Tags.html) each object using the Unity Editor. Then call `FindGameObjectsWithTag` to find the collection of tagged game objects:

	var pedestrians = GameObject.FindGameObjectsWithTag("Pedestrian");
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found pedestrian: " + pedestrian.name);
	}

## 5. Find object by type

Finding objects by name is best avoided where possible. It can lead to fragile code. If you find yourself having [*magic strings*](https://en.wikipedia.org/wiki/Magic_string) in your code you should consider finding by type instead:

	var leaderboard = GameObject.FindObjectOfType<Leaderboard>();
	Debug.Log("Found leaderboard: " + leaderboard.name);	

This can lead to better code because it will be more [type-safe](https://en.wikipedia.org/wiki/Type_safety). This kind of code can surive automatic [refactoring](https://en.wikipedia.org/wiki/Code_refactoring) (for example in [Visual Studio](http://www.what-could-possibly-go-wrong.com/unity-and-visual-studio/)). 

## 6. Find objects by type

Unity conveniently gives you a function to find multiple objects by type:

	var pedestrians = GameObject.FindObjectsOfType<Pedestrian>();
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found pedestrian: " + pedestrian.name);
	}

I love the fact that you can easily chain [LINQ](https://en.wikipedia.org/wiki/Language_Integrated_Query) functions on the end of this. For example if you want to sort the objects by name:  

	var sortedPedestrians = GameObject
		.FindObjectsOfType<Pedestrian>()
		.OrderBy(pedestrian => pedestrian.name);

Of if you want to first filter out certain game objects:

	var sortedPedestrians = GameObject
		.FindObjectsOfType<Pedestrian>()
		.Where(pedestrian => PedestrianMatches(pedestrian))
		.OrderBy(pedestrian => pedestrian.name);

# What can't we do with the Unity API?

So what can't we do with the native Unity API?

- Well for a start you can't get multiple objects by name (unless you write your own function, as I demonstrated earlier).
- It's not possible to search for an object by name *somewhere* under another object. As I've demonstrated you can find an object directly under a parent, you just can't find an object in the entire sub-hierarchy. 
- You definitely can't search for multiple objects by name *somewhere* under a parent object.
- You can't do partial name or wildcard matching, for example you can't find all objects that have a name starting with *Pedestrian*.
- We can search for multiple objects by *tag*, but we can't search for them by *layer*.
- We can't combine our queries in sophisticated ways for more advanced searches.

So how do we achieve these feats? Enter the Scene Query library... 

# Finding game objects with the Scene Query API

The [Scene Query library](https://github.com/Real-Serious-Games/Unity-Scene-Query) provides basic search facilities like we have already discussed. It also provides numerous advanced features on top of what Unity can already do.

I'll start by covering much of the same ground and showing you how to do basic stuff with the Scene Query library. We'll quickly work our way to the more advanced features.  


## Getting setup

You can copy the [code from github](https://github.com/Real-Serious-Games/Unity-Scene-Query) and copy it into your project or you can get the [dll from nuget](https://www.nuget.org/packages/RSG.SceneQuery/).

You can find the complete code in the [example Unity project](https://github.com/Real-Serious-Games/Unity-Scene-Query-Examples) as well under the *Scene Query Library* folder. Just copy this folder into your own project.

To use the library simply instantiate `SceneQuery`:

	SceneQuery sceneQuery = new SceneQuery();

## Basic Features 

In the following sections I cover the basic feature of Scene Query. If you are eager to get to the cool stuff please jump straight to the [Advanced Features](#advancedfeatures) section, you can always come back and read the basics later. 

### 7. Find object by name

Finding a single object by name is achieved through `SelectOne`:

	var player = sceneQuery.SelectOne("Player");
	Debug.Log("Found game object " + player.name);

So far there is little difference between Scene Query and the native Unity API (besides the name of the function).

Note that this finds the first object with the requested name. If there are no objects with that name it returns `null`.

Alternatively you could `ExpectOne`:

	var player = sceneQuery.ExpectOne("Player);

This throws an exception when the requested game object does not exist or when there are more than a single instance of the name object. It can simplify your error handling and make your code more expressive of your intent.

### 8. Find objects by name

`SelectAll` finds all objects with a given name:

	var pedestrians = sceneQuery.SelectAll("Pedestrian");
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found game object " + pedestrian.name);
	}

Now we are getting somewhere. There is no native Unity function for getting a collection of objects by name.

### 9. Find object directly under a named parent

Let's look at something else the Unity API can't do by itself. We can find an object by its *path* in the hierarchy:

	var child = sceneQuery.SelectOne("Parent/Child");
	Debug.Log("Found child object: " + child.name);

Note that the *hierarchy path* looks similar to a *filesystem path*. We can address objects by path down to any depth of the hierarchy, for example:

	var child = sceneQuery.SelectOne("Parent/Intermediate1/Intermediate2/Child");
	Debug.Log("Found child object: " + child.name);

### 10. Find objects directly under a named parent

You can also search for multiple objects that match a particular path. This example finds all children of *Parent* that have the name *Child*: 

	var children = sceneQuery.SelectAll("Parent/Child");
	foreach (var child in children)
	{
		Debug.Log("Found child object: " + child.name);
	}

### 11. Find object in sub-hierarchy

We are getting more advanced now. Let's delve into the *Scene Query language*. Here I use the `>` operator to find an object that is *somewhere* under another named object:  

	var child = sceneQuery.SelectOne("Parent>Child");
	Debug.Log("Found child object: " + child.name);

This finds an object named *Child* that is somewhere, but not necessarily directly, under an object named *Parent*. Note that additional spaces don't matter:

	var child = sceneQuery.SelectOne("Parent > Child");

### 12. Find objects in sub-hierarchy

You can also use the `>` operator to find multiple objects somewhere under another object:

	var children = sceneQuery.SelectAll("Parent>Child");
	foreach (var child in children)
	{
		Debug.Log("Found child object: " + child.name);
	}

### 13. Find objects by layer or tag

You can find all objects on a particular tag or layer by using the `.` operator:  

	var pedestrians = sceneQuery.SelectAll(".Pedestrian");
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found pedestrian: " + pedestrian.name);
	}

That finds all objects that are tagged with *Pedestrian*.

Note that you can find objects by tag in Unity, you can't actually find objects by layer natively. The nice thing here is that you get a unified syntax for finding by both tag and layer.

### 14. Find object by type

Scene query wouldn't be complete without allowing you to find objects by component type: 

	var pedestrian = sceneQuery.SelectOne<Pedestrian>();
	Debug.Log("Found pedestrian: " + pedestrian.name);

### 15. Find objects by type

You can also find multiple objects by type:

	var pedestrians = sceneQuery.SelectAll<Pedestrian>();
	foreach (var pedestrian in pedestrians)
	{
		Debug.Log("Found pedestrian: " + pedestrian.name);
	}

Of course you can find one or multiple objects by type using the native Unity API, you don't need Scene Query if that's all you need. However what you don't get with Unity is the ability to combine queries, something that I'll explain soon.

### Finding nested objects when you already have the parent object

Scene Query comes with a number of `GameObject` [extension methods](https://msdn.microsoft.com/en-AU/library/bb383977.aspx?f=255&MSPPError=-2147217396). This means you can call the varieties of `SelectOne` and `SelectAll` directly on a game object when you want to search the sub-hierarchy beneath that object.

For example, searching for a single sub-hierarchy object:

	GameObject parentGameObject = ... some parent object ...
	GameObject child = someParentObject.SelectOne("some-child");

Or searching for multiple:

	GameObject parentGameObject = ... some parent object ...
	var children = someParentObject.SelectAll("some-child");

You can also search a sub-hierarchy type and make use of the other advanced features that are coming up next.

## Advanced Features

In the following sections I discuss the more advanced features of the Scene Query library. This is the really cool stuff.

### Combining queries

Scene query allows you to combine queries. For example, searching by name and tag:

	var pedestriansOnTheRoad = sceneQuery.SelectAll("Pedestrian.Road");

Can you see how it's starting to look like CSS?

Searching by name and tag directly under a particular parent object:

	var pedestriansOnTheRoadInSection1 = sceneQuery.SelectAll("Section1/Pedestrian.Road");

Or searching somewhere in a sub-hierarchy:

	var pedestriansOnTheRoadInSection1 = sceneQuery.SelectAll("Section1>Pedestrian.Road");

How about all *Pedestrians* under objects with the *Road* tag: 

	var pedestriansOnTheRoad = sceneQuery.SelectAll(".Road > Pedestrian");

You can also add particular types to the mix. This next example finds all objects with a `Pedestrian` component that are somewhere under a parent object that is tagged as *Road*:

	var pedestriansOnTheRoad = sceneQuery.SelectAll<Pedestrian>(".Road > ?.*"); 

That last one uses a partial name match, which I'll discuss in the next section.

What if you want everthing except a particular set of game objects? You can use the `!` operator to invert any query:

	var everythingButPedestrians = sceneQuery.SelectAll("! .Road > Pedestrian");

There are many potential combinations of queries, This is just a taste of what's possible.

These advanced queries also work when we already have a parent object and want to search the sub-hierarchy, for example finding non-pedestrians that are on the road:

	GameObject road = ... some road game object ...
	var nonPedestrians = road.SelectAll("!Pedestrian");
	 
### Partial matching and wildcards

Scene Query allows for partial name matching and the use of wildcards.

For example, say you want to find all objects that have *Pedestrian* in the name:

	var pedestrians = sceneQuery.SelectAll("?Pedestrian");

This will find objects named Pedestrian1, Pedestrian2, Pedestrian_Fred, etc.

The `?` operator enables [*regular expression pattern matching*](https://en.wikipedia.org/wiki/Regular_expression). You can use any regular expression to define the names of the objects to match. From the simplest *match anything* wild card:

	 var everything = sceneQuery.SelectAll("?.*");

To much more complicated patterns:

	var selectedPedestrians = sceneQuery.SelectAll("?^Pedestrian[0-9]$*");

To make sophisticated use of this you will have to learn more about regular	expressions.

Partial matching and wildcards can also be used with tags and layers:

	var allRoads = sceneQuery.SelectAll(".?Road");

That finds all objects that have a tag that contains *Road*, eg Road1, Road2, Road_X etc.

This can also be used with *hierarchy paths* and the `>` operator:

	var pedestriansOnRoads = sceneQuery.SelectAll("?Road > ?Pedestrian");

That finds all objects with *Pedestrian* in the name that are somewhere under objects with *Road* in the name.

The `?` operator can also be used with sub-hierarchies:

	GameObject road = ... some road game object ...
	var pedestrians = road.SelectAll("?Pedestrian");

### Combination with scene traversal and LINQ

Scene Query functions can be used with [scene traversal](http://www.what-could-possibly-go-wrong.com/scene-traversal-recipes-for-unity/) and LINQ functions. 

This example finds all descendents of any object whose name contains *Pedestrian*:

	var pedestrianDescendents = sceneQuery.SelectAll("?Pedestrian")
		.SelectMany(pedestrian => pedestrian.Descendents())
		.ToArray();  

This example finds all descendents and filters for those that do have a mesh: 

	var pedestrianMeshes = sceneQuery.SelectAll("?Pedestrian")
		.SelectMany(pedestrian => pedestrian.Descendents())
		.Where(child => child.GetComponent<MeshRenderer>() != null)
		.ToArray(); 

There is so much more you can do with by combining Scene Query, scene traversal and LINQ.

### Finding objects with spaces and special characters in the name

Because the *Scene Query language* is a language (it even has an [EBNF grammar spec](https://github.com/real-serious-games/unity-scene-query#ebnf)) it has some problems with particular object names. For example you can't just have an object name with a space or a question-mark as these [tokens](https://en.wikipedia.org/wiki/Lexical_analysis#Token) have special meaning in the query language.

To represent these kinds of names you must use quotes.

For example let's say there is a game object with the name *Pedestrian A*. To query for this object we must enclose it's name in quotes:

	var pedestrian = sceneQuery.SelectOne("'Pedestrian A'");

You can use either single or double quotes, however single quotes are easier to type and clearer in this case as we can include them in the C# [string literal](https://en.wikipedia.org/wiki/String_literal) without having to use an [escape-sequence](https://en.wikipedia.org/wiki/Escape_sequence). 

Note that any of the more advanced queries can still be applied:

	var pedestrian = 
		sceneQuery.SelectOne("'Road X' > 'Pedestrian A'.HoldingAnApple");

# Performance considerations

Querying the scene (no matter how you do it) can be very slow and it's generally not something you want to do frequently at runtime. It's best to execute your scene queries at start/load time and cache the results. Just be warned... if you try to do this in an update or render function you might find that your framerate slows to a crawl. 

Use these techniques sparingly and at appropriate times.  

# Conclusion

In this article I've overviewed my Scene Query library and how it can be used to query the Unity hierarchy in a way similar to CSS selectors or was if the scene was some kind of database. 

I've shown the basics of searching for objects using the native Unity API. Then I showed how Scene Query can do this but also that it has so much more to offer. Scene Query is an expressive and flexible language for pattern matching to find game objects. 

Hopefully I've show how combining queries to specify exactly what you are searching for can be such a powerful technique. 

Thanks for reading.



