---
layout: post
title: Using promises for Unity async operations
date: '2016-07-08 22:45:37'
permalink: using-promises-for-unity-async-operations
---

Are you struggling to understand how to use [promises](https://en.wikipedia.org/wiki/Futures_and_promises) in Unity? This article demonstrates, example by example, how to represent Unity's common asynchronous operations as promises.   

I've already written extensively about using [promises for game development](http://www.what-could-possibly-go-wrong.com/promises-for-game-development/). That earlier article covers background, theory, the benefits of using promises and generally how to apply promises for game development. 

In this article I take it back a notch and provide simple and practical demonstrations of using promises with Unity.   

Before we get to the examples, I'll quickly cover the basics of Unity async operations. Then we'll work though the examples. At the end I bring it altogether in some more complex examples that demonstrate the power of promises to compose chained asynchronous operations.

A Unity project with the example code is available on github: [https://github.com/Real-Serious-Games/Unity-Async-and-Promises](https://github.com/Real-Serious-Games/Unity-Async-and-Promises)

# Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [My setup](#mysetup)
- [Why async operations?](#whyasyncoperations)
- [Introducing Unity async operations](#introducingunityasyncoperations)
- [Why promises?](#whypromises)
- [Examples of coroutines as promises](#examplesofcoroutinesaspromises)
  - [1. Basic template for a coroutine as a promise](#1basictemplateforacoroutineasapromise)
  - [2. Getting a result when the coroutine has completed](#2gettingaresultwhenthecoroutinehascompleted)
  - [3. Making a HTTP GET request (using WWW) as a promise](#3makingahttpgetrequestusingwwwasapromise)
  - [4. Singleton instance of the GameObject/MonoBehavior](#4singletoninstanceofthegameobjectmonobehavior)
  - [5. Loading a scene asynchronously](#5loadingasceneasynchronously)
  - [6. Loading an asset bundle](#6loadinganassetbundle)
  - [7. Combined example](#7combinedexample)
  - [8. Example merging multiple scenes - hardcoded](#8examplemergingmultiplesceneshardcoded)
  - [9. Example merging multiple scenes - dynamic](#9examplemergingmultiplescenesdynamic)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# My setup

I'm currently using Unity 5.3.4f1, although all of these examples should work for previous versions.

For code editing and compilation outside of Unity I'm using [Visual Studio 2015 Community](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx).

# Why async operations?

The first question: why use asynchronous operations? 

In many cases we don't actually have to use them. Unity provides synchronous and asynchronous functions for loading scenes and asset bundles. So we do have a choice.

Mostly you want to use asynchronous operations to avoid blocking the main thread. If you load an asset synchronously your game will stop updating and rendering! This will appear to [hang your game](https://en.wikipedia.org/wiki/Hang_(computing)) and it will become unresponsive. Of course it will come back to life once the synchronous load has finished, however in the meantime it's not a good [user experience](https://en.wikipedia.org/wiki/User_experience) and the player may rightly think your game has a bug! 

When you use asynchronous operations your game will continue to operate while the load is happening (which can sometimes be a lengthy delay). This means you can display an animated loading screen (or maybe a game play feature) during the loading.

[Open world games](https://en.wikipedia.org/wiki/Open_world) with streaming content *rely* on asynchronous operations. It just won't do to have the game hang as distant geometry is loaded while the player moves through the world.   

# Introducing Unity async operations

Asynchronous operations in Unity are typically achieved via [Unity coroutines](http://docs.unity3d.com/Manual/Coroutines.html), although on occasion I've seen them handled via an [update function](http://gameprogrammingpatterns.com/update-method.html) that [polls](https://en.wikipedia.org/wiki/Polling_(computer_science)) for a status of *load completed*.

The simplest coroutine can be boiled down to this:

	using RSG;

	public class BasicCoroutine : MonoBehaviour
	{
		public void StartTheCoroutine(... parameters ...) 
		{
            StartCoroutine(TheCoroutine(... parameters ...))
		}

        private IEnumerator TheCoroutine(... parameters ...)
        {
			var someAsyncOperation = ... creates the async operation ...

	        yield return someAsyncOperation;

			// ... some time later this code will continue to 
			// execute after the async operation has completed ...
        }
	}

This is what is happening:

1. Define a function that returns an *[IEnumerator](https://msdn.microsoft.com/en-us/library/system.collections.ienumerator(v=vs.90).aspx)*. This function is the *body* of your coroutine.
2. Call that function and pass the result to *[StartCoroutine](http://docs.unity3d.com/ScriptReference/MonoBehaviour.StartCoroutine.html)*. 
3. Start your async operation. To be used with coroutines it should be expressed in the form of an *[AsyncOperation](http://docs.unity3d.com/ScriptReference/AsyncOperation.html)* object. *[Yield](http://docs.unity3d.com/400/Documentation/ScriptReference/index.Coroutines_26_Yield.html)* this object.
4. The coroutine will now [block](https://en.wikipedia.org/wiki/Blocking_(computing)) until the async operation has completed, after which it will resume execution from where it left off.

So that was the basic template for an async operation coroutine. Now let's look at a realistic example. 

This example shows a [HTTP GET](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) via Unity's [WWW](http://docs.unity3d.com/ScriptReference/WWW.html) class. 

I am starting with this example because it is quite simple, but can also be very useful, for example when you need to pull [JSON](https://en.wikipedia.org/wiki/JSON) data from a [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer).    

	public class CoroutineWithHttpGet : MonoBehavior
	{
        private IEnumerator TheCoroutine(string url)
        {
            var www = new WWW(url);

            yield return www; // Allow the async operation to complete.

            if (!string.IsNullOrEmpty(www.error))
            {
				// ... an error occurred, handle it ...
                return;
            }

			//
			// Successfully got a response via HTTP GET.
			//
            var response = www.text;

			// ... do something with the response ...

			// If the data retrieved is JSON, you can deserialize it here.

			Debug.Log(response); 
        }
	}

Now start the coroutine with the URL for the REST API:

	StartCoroutine(TheCoroutine("http://some-host/some-rest-api"));

You can easily copy-and-paste and test this example in Unity. Try it with a real web site: 

	StartCoroutine(TheCoroutine("http://www.google.com"));

When you run this you should see a bunch of HTML dumped to your log. 

I don't want to go into detail here on the workings of coroutines, I'll save that for a future article. Suffice to say that in those examples everything after the *yield* statement is executed asynchronously. 

# Why promises?

The point of this article is to demonstrate how to express async operations and coroutines as promises. Let's consider for a moment why we would want this. 

Actually, this is already covered in [the coroutines section of promises for game development](http://www.what-could-possibly-go-wrong.com/promises-for-game-development/#promisesvsunitycoroutines).

Suffice to say that I'm not a huge fan of coroutines and that I'd prefer to manage asynchronous operations with promises. Coroutines do have some benefits, but I believe the disadvantages outweigh the advantages. When you start using promises you'll have much more flexibility in composing complex chains of async operations.

However we must use coroutines at some level! This is how most async operations are done in Unity (there are otherways to do this, I'll talk about threads in a separate article). Hence the need for this article: how do we use coroutiones *as* promises? 

It is easy enough to do this: 

- Simply create a promise object and pass it into the coroutine. 
- If the coroutine is successful, then *resolve* the promise. 
- Otherwise *reject* the promise. 

# Examples of coroutines as promises 

Let's move onto the examples of using coroutines as promises. We'll start with a couple of simple templates that you can use as a starting point. Then I'll cover practical examples such as loading scenes and asset bundles. At the end I present more complex examples of chaining together multiple asynchronous operations.

To follow along please download [the example project](https://github.com/Real-Serious-Games/Unity-Async-and-Promises) from github. The example project has working code for each of the examples presented here.

## 1. Basic template for a coroutine as a promise 

The key to all examples here is being able to express a basic coroutine as a promise. This example is a non-specific template that you can use for this. It demonstrates the basic pattern that we build on in subsequent examples.

	using RSG;

	public class BasicCoroutineAsPromise : MonoBehaviour
	{
		public IPromise Execute() 
		{
			// Create the promise object.
			return new Promise((resolve, reject) =>
				// Pass the promise to the coroutine.
                StartCoroutine(TheCoroutine(resolve, reject))
            );						
		}

        private IEnumerator TheCoroutine(
			Action resolve, 
			Action<Exception> reject
		)
        {
			// ... add your async operations here ...

	        yield return yourAsyncOperation;

			// ... several yields later ...

	        var someErrorOccurred = ... did an error occur ...
			if (someErrorOccurred) 
			{
				// An error occurred, reject the promise.
                reject(someException);
			}
			else 
			{
				// Completed successfully, resolve the promise.
                resolve();
            }
        }
	}

That's it! 

This simple example performs the following steps:

- The *Execute* method invokes the async operation. 
- A promise object is created. 
- The parameter to the `Promise` constructor is an [anonymous function](https://msdn.microsoft.com/en-AU/library/bb882516.aspx) that is passed `resolve` and `reject` functions. These functions are how we interact with the promise. 
- The coroutine is started.
- Eventually the promise is *resolved* or *rejected* depending on the outcome of the coroutine.

Let's invoke that code and chain `Then` and `Catch` callbacks:

	var exampleCoroutine = 
		someGameObject.AddComponent<BasicCoroutineAsPromise>();

	exampleCoroutine.Execute()
		.Then(() =>
		{
			// Coroutine completed succesfully and yielded a result.
		})
		.Catch(ex =>
		{
			// Coroutine failed with an error!
		});
 
The `Then` callback is invoked when the promise is resolved. 

`Catch` is invoked when the promise is rejected.

## 2. Getting a result when the coroutine has completed

The first example assumes that we aren't getting a result from the coroutine. This is actually more useful than you might think... often you do want to fire off a scene load or some background processing that doesn't have an explicit result. All you want is the callback or error handling after the process has completed.  

Other times though you will need to retrieve a result. An example of this would be a HTTP request or loading an *asset bundle*. At the end of that process you need to get something back that you can work with. For this you need to make use of the *generic* promise that can be resolved to a particular value.

Again this example is a non-specific template:

	public class CoroutineResult : MonoBehaviour
	{
	    public IPromise<string> Execute()
	    {
	        return new Promise<string>((resolve, reject) =>
	            StartCoroutine(TheCoroutine(resolve, reject))
	        );
	    }
	
	    private IEnumerator TheCoroutine(
			Action<string> resolve, 
			Action<Exception> reject
		)
	    {
	        // ... add your async operations here ...
	
	        yield return yourAsyncOperation;
	
	        // ... several yields later ...
	
	        var someErrorOccurred = ... did an error occur ...
	        if (someErrorOccurred)
	        {
	            // An error occurred, reject the promise.
	            reject(new ApplicationException("My error"));
	        }
	        else
	        {
	            // Completed successfully, 
				// resolve the promise to return a particular value.
	            resolve("Hi from the coroutine!");
	        }
	    }
	}

In this example the promise is resolved to a hard-coded string value, this is just to make the example simple. We'll get to a real example in a moment.

This is how to invoke that code. It is very similar to the previous example with the exception that the `Then` callback is passed the value of the resolved promise: 

	var exampleCoroutine = gameObject.AddComponent<CoroutineResult>();
	exampleCoroutine.Execute()
	    .Then(value => 
		{
			Debug.Log("Coroutine has completed with value: " + value); 
		})
	    .Catch(ex => 
		{
			Debug.LogException(ex, this); 
		});

## 3. Making a HTTP GET request (using WWW) as a promise

Ok... now it's time for a more realistic example. This example demonstrates how to *promisify* a [HTTP GET](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) request using Unity's [WWW](http://docs.unity3d.com/ScriptReference/WWW.html) class.

This is a simple extension of the previous HTTP GET example. The promise resolves to a string that is the body of the web page that is retrieved. This could be simple text, it could be a CSV file or it could be [JSON](https://en.wikipedia.org/wiki/JSON) data.  

	public class CoroutineWithHttpGet : MonoBehaviour
	{
	    public IPromise<string> Get(string url)
	    {
	        return new Promise<string>((resolve, reject) =>
	            StartCoroutine(TheCoroutine(url, resolve, reject))
	        );
	    }
	
	    private IEnumerator TheCoroutine(
			string url, 
			Action<string> resolve, 
			Action<Exception> reject
		)
	    {
	        var www = new WWW(url);
	
	        yield return www; // Allow the async operation to complete.
	
            if (!string.IsNullOrEmpty(www.error))
            {
                reject(new ApplicationException(www.error));
            }
            else
            {
                resolve(www.text);
            }
	    }
	}

In this example I've assumed that the data retrieved is text. It could just as easily be binary data. In that case we'd resolve the promise to a byte array or some kind of *buffer* object that represents the data.

Here is how the code is used:

    var exampleCoroutine = gameObject.AddComponent<CoroutineWithHttpGet>();
    exampleCoroutine.Get("http://www.google.com")
        .Then(value => 
		{
			Debug.Log("HTML: " + value); 
		})
        .Catch(ex => 
		{
			Debug.LogException(ex, this);
		});

Now let's add JSON [deserialization](https://en.wikipedia.org/wiki/Serialization) into the mix. 
Let's assume we are talking to a REST API that returns it's data in JSON format (which is a common way to do this sort of thing). 

To make the example more realistic let's say we are retrieving our game's leaderboard from a cloud server. 

	public class Leaderboard 
	{
		// Class defined for deserialization.
	}

    exampleCoroutine.Get("http://the-games-server.com/leaderboard-rest-api")
        .Then(jsonData => JsonConvert.DeserializeObject<Leaderboard>(jsonData))
		.Then(leaderboard => ... do something with the leaderboard ...)
        .Catch(ex => Debug.LogException(ex, this));
	
In this example I'm using [JSON.NET](http://www.newtonsoft.com/json). You can find a [Unity fork of JSON.NET here](https://github.com/codecapers/Unity.Newtonsoft.Json). JSON.NET is great, but difficult to get working under Unity. If you are using Unity 5.3 or above I highly recommend that you try the [built-in JSON serialization](http://docs.unity3d.com/Manual/JSONSerialization.html) (which they have finally added in version 5!!!).

You may never have seen [chaining of promises](http://odetocode.com/blogs/scott/archive/2015/09/28/chaining-promises-in-javascript.aspx) before, if that's the case hopefully you can start to appreciate it. If something goes wrong anywhere along the chain the `Catch` handler is invoked. This greatly simplifies asynchronous error handling in a way that we should already be familiar, it is very similar to [regular exception handling](https://en.wikipedia.org/wiki/Exception_handling). 

## 4. Singleton instance of the GameObject/MonoBehavior

Now let's change the general template to a [singleton](https://en.wikipedia.org/wiki/Singleton_pattern) to make it more convenient. This is the same as the previous HTTP example, however it lazily generates a singleton GameObject the first time it is called. On subsequent calls the singleton already exists and so it is reused.

	public class SingletonInstance : MonoBehaviour
	{
	    //
	    // Singleton instances used to run the coroutine.
	    //
	    private static SingletonInstance singletonInstance = null;
	
	    public static IPromise<string> Get(string url)
	    {
	        if (singletonInstance == null)
	        {
				// Singleton not yet created, create it now and reuse it later.
	            var gameObject = new GameObject("_HTTP_Helper");
	            GameObject.DontDestroyOnLoad(gameObject);
	            singletonInstance = 
					gameObject.AddComponent<SingletonInstance>();
	        }
	
	        return singletonInstance.PrivateGet(url);
	    }
	
	    private IPromise<string> PrivateGet(string url)
	    {
	        return new Promise<string>((resolve, reject) =>
	            StartCoroutine(TheCoroutine(url, resolve, reject))
	        );
	    }
	
	    private IEnumerator TheCoroutine(
			string url, 
			Action<string> resolve, 
			Action<Exception> reject
		)
	    {
	        ... same as before ...
	    }
	}

Note that the game object and the component are created procedurally in the `Get` function. There is also a call to [`DontDestroyOnLoad`](http://docs.unity3d.com/ScriptReference/Object.DontDestroyOnLoad.html). This ensures that our singleton game object isn't unloaded when new a scene is loaded, so we'll have a true singleton that will continue to remain in memory. 

Notice that the name of the GameObject singleton starts with an underscore. I use the underscore character as a simple mechanism to highlight game objects in the [Unity hierarchy](http://docs.unity3d.com/Manual/Hierarchy.html) that were created procedurally. This means I can visually check the hierarchy in the Unity Editor and quickly identify objects that are created from code as opposed to those created manually in the Unity Editor.

So how do we use this? Note that the following example of use is simplified from previous examples. Now that we have the singleton automatically created it becomes a tad easier to use: 

    SingletonInstance.Get("http://www.google.com")
        .Then(value => Debug.Log("HTML: " + value))
        .Catch(ex => Debug.LogException(ex, this));

You might rightly argue that singleton is a terrible [design pattern](https://en.wikipedia.org/wiki/Design_pattern). Normally I might agree with you, however due to the bizarre way that the Unity framework is structured (at least from the coder's perspective) singletons are a simple and appealing way to work. Difficulties arise when you scale up to the creation of bigger and more complex applications, when this happens you will have to learn new techniques, such as [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection), for managing the complexity. That's something I can talk more about in a future article.

So besides convenience, what else do we get from using the singleton? Well you'll notice that we are ever so slightly more decoupled from the Unity API and [loose coupling](https://en.wikipedia.org/wiki/Loose_coupling) often equates to better code design (although anything used to the extreme can also be destructive). We are calling a static function on a MonoBehavior and getting a promise back to manage the asynchronous operation. We could easily put this behind a non-MonoBehaviour and be even more decoupled. Going further we could use an interface that allows *dependency injection*. At this point our code may not even know if is Unity under the hood and hence we are fully decoupled from the Unity API. How do we do this and why is it a good thing? We'll to start it might mean that you can *more* easily change game engines, for example if we are completely decoupled it should be possible to move your code to [MonoGame](https://en.wikipedia.org/wiki/MonoGame). Ok I'm digressing now... I should definitely come back to this in a future article.

## 5. Loading a scene asynchronously

The next example shows how to load a Unity scene asynchronously as a promise. 

	public class SceneLoader : MonoBehaviour
	{
	    private static SceneLoader singletonInstance = null;
	
	    /// <summary>
	    /// Load a named scene, returns a promise that is resolved 
		/// when the scene has loaded.
	    /// </summary>
	    public static IPromise LoadScene(string sceneName)
	    {
	        if (singletonInstance == null)
	        {
	            var gameObject = new GameObject("_SceneLoader");
	            GameObject.DontDestroyOnLoad(gameObject);
	            singletonInstance = gameObject.AddComponent<SceneLoader>();
	        }
	
	        return singletonInstance.PrivateLoadScene(sceneName);
	    }
	
	    private IPromise PrivateLoadScene(string sceneName)
	    {
	        return new Promise((resolve, reject) =>
	            StartCoroutine(TheCoroutine(sceneName, resolve, reject))
	        );
	    }
	
	    private IEnumerator TheCoroutine(
			string sceneName, 
			Action resolve, 
			Action<Exception> reject
		)
	    {
	        yield return Application.LoadLevelAsync(sceneName);
	
	        resolve();
	   }
	}

The code is invoked as follows:

    SceneLoader.LoadScene("Some-Scene")
        .Then(() => Debug.Log("Loaded scene."))
        .Catch(ex => Debug.LogException(ex, this));
	

Make sure that you add the scene to your [build settings](http://docs.unity3d.com/Manual/BuildSettings.html), otherwise you'll get an error like this:

	Scene 'Some-Scene' (-1) couldn't be loaded because it has not been added to the build settings or the AssetBundle has not been loaded.
	UnityEngine.Application:LoadLevelAsync(String)
	<TheCoroutine>c__Iterator4:MoveNext() (at Assets/5. SceneLoader/SceneLoader.cs:47)
	...

That example uses [Application.LoadLevelAsync](http://docs.unity3d.com/ScriptReference/Application.LoadLevelAsync.html). You can just as easily use [LoadLevelAdditiveAsync](http://docs.unity3d.com/ScriptReference/Application.LoadLevelAdditiveAsync.html) as is demonstrated in example 8.

If you are using Unity 5.3 or above you'll find that those functions are now obsolete. Instead you'll need to use the new [SceneManager functions](http://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.html), which work in basically the same way.

## 6. Loading an asset bundle 

This example shows how to asynchronously load an [asset bundle](http://docs.unity3d.com/Manual/AssetBundlesIntro.html) then instantiate game objects from the bundle into the scene. This example is interesting because I can demonstrate chaining of promises to do the instantiation after the asset bundle is loaded. 

To make this work you need to create an asset bundle. You can work through the [Unity docs for this](http://docs.unity3d.com/Manual/BuildingAssetBundles.html), however I'll deal with it briefly here.

Use the Unity Editor to mark prefabs that are to be included in bundles. 

I've pre-created some bundles that are included with the example project. [According to the Unity manual](http://docs.unity3d.com/Manual/BuildingAssetBundles.html) you need to use something like the following code to create an asset bundle. 

	using UnityEditor;

	public class AssetBundleCreator
	{
	    [MenuItem("Custom/Build Asset Bundles")]
	    static void BuildAllAssetBundles()
	    {
	        BuildPipeline.BuildAssetBundles(
				Path.Combine(Application.dataPath, "<sub-directory>")
			);
	    }
	}

I honestly can't understand why Unity makes you add such a trivial amount of code to enable such a crucial feature as asset bundles. I can't fathom why they don't just include this menu item in the Unity Editor by default!

Note that the `AssetBundleCreator` script must be stored in an *Editor* sub-directory. It's code that you want to invoke only from the Unity Editor, you don't want this in your build. 

Now let's see how to load a bundle expressed as a promise:

	public class AssetBundleLoader : MonoBehaviour
	{
	    private static AssetBundleLoader singletonInstance = null;
	
	    /// <summary>
	    /// Initiatiates an asynchronous load of an asset bundle.
		/// Returns a promise that is resolved to the handle of the bundle.
	    /// </summary>
	    public static IPromise<AssetBundle> LoadAssetBundle(
			string assetBundleFilePath
		)
	    {
	        if (singletonInstance == null)
	        {
	            var gameObject = new GameObject("_AssetBundleLoader");
	            GameObject.DontDestroyOnLoad(gameObject);
	            singletonInstance = 
					gameObject.AddComponent<AssetBundleLoader>();
	        }
	
	        return singletonInstance
				.PrivateLoadAssetBundle(assetBundleFilePath);
	    }
	
	    private IPromise<AssetBundle> PrivateLoadAssetBundle(
			string assetBundleFilePath
		)
	    {
	        return new Promise<AssetBundle>((resolve, reject) =>
	            StartCoroutine(TheCoroutine(
					assetBundleFilePath, 
					resolve, 
					reject
				))
	        );
	    }
	
	    private IEnumerator TheCoroutine(
			string assetBundleFilePath, 
			Action<AssetBundle> resolve, 
			Action<Exception> reject
		)
	    {
	        var www = new WWW("file://" + assetBundleFilePath);
	
	        yield return www;
	
	        if (!string.IsNullOrEmpty(www.error))
	        {
				var msg = "Failed to load asset bundle " + 
						  assetBundleFilePath + "\r\n" + www.error;
                reject(new ApplicationException(msg));
	        }
	        else 
	        {
	        	resolve(www.assetBundle);
	        }
        }
	}
	
Now we can load the bundle and *chain* instantiation of game objects: 

    var assetBundleFilePath = 
		Path.Combine(Application.dataPath, "<sub-directory>");
    
	AssetBundleLoader.LoadAssetBundle(assetBundleFilePath)
        .Then(assetBundle =>
        {
            Debug.Log("Loaded bundle.");

            var allBundleObjects = assetBundle
				// Load all game objects from the bundle.
                .LoadAllAssets(typeof(GameObject))

				// Cast all objects to the expected type. 
                .Cast<GameObject>(); 

            var x = 0f;

			// For simplicity let's just load all objects from the 
			// bundle and place them in a row.
            foreach (var bundleObject in allBundleObjects)
            {
                Instantiate(
					bundleObject, 
					new Vector3(x, 0f, 0f), 
					Quaternion.identity
				);
                x += 3f;
            }                
            
			// Unload the bundle, keep loads objects in tact.
            assetBundle.Unload(false); 
        }) 
        .Catch(ex => Debug.LogException(ex, this));

In this example we naively instantiate all bundle objects into the scene, this is a contrived example to demonstrate the technique and you can easily find a better way to structure it. 

In this example the bundle is unloaded after the game objects are instantiated. Note that instead of this you might want to cache the bundle if you intend to load more objects from it later. 

## 7. Combined example

Now it's time to start putting together the examples. Here you can start to realize the power of chained promise for composing asynchronous logic.

In this example I've taken the liberty of [refactor extracting](http://c2.com/cgi/wiki?ExtractMethod) some helper functions. This makes it easy to see the chain of promises and makes the logic very clear. Code that is easy to understand is code where bugs have difficulty hiding. 

        LoadScene()
            .Then(() => LoadBundle())
            .Then(assetBundle => InstantiateGameObjects(assetBundle))
            .Then(assetBundle => assetBundle.Unload(false))
       		.Catch(ex => Debug.LogException(ex, this));

To see how the helper methods work, please see the [full example in github](https://github.com/Real-Serious-Games/Unity-Async-and-Promises/blob/master/Assets/7.%20Combined/CombinedExample.cs). 

## 8. Example merging multiple scenes - hardcoded

This example uses additive scene loading to merge multiple scenes, here's the most important bit:

        AdditiveSceneLoader.LoadScene("MergeScene1")
            .Then(() => AdditiveSceneLoader.LoadScene("MergeScene2"))
            .Then(() => AdditiveSceneLoader.LoadScene("MergeScene3"))
            .Catch(ex => Debug.LogException(ex, this));

Again this uses the pre-Unity 5.3 API, but is simple to convert to Unity 5.3 or above.
	
## 9. Example merging multiple scenes - dynamic

That previous example was hard-coded. It is more useful to load a dynamically generated collection of scenes. There are numerous ways to achieve this. 

Here I generate an array (it could just as easily be a list though) then use [LINQ](https://en.wikipedia.org/wiki/Language_Integrated_Query) `Aggregate` to collapse the list into a sequence of promises. This chains the loading so one scene is loaded, then the next and so on until all scenes are loaded. 

    var scenesToLoad = new string[]
    {
        "MergeScene1",
        "MergeScene2",
        "MergeScene3",
    };

    scenesToLoad.Aggregate(
			Promise.Resolved(),
            (prevPromise, sceneName) => 
				prevPromise.Then(() => 
					AdditiveSceneLoader.LoadScene(sceneName))
        )
        .Then(() => Debug.Log("Loaded all scenes."))
        .Catch(ex => Debug.LogException(ex, this));

If you are now thinking... *whoa, I didn't sign up for something that scary looking*, here's an easier to read version using `Promise.All`:

    var scenesToLoad = new string[]
    {
        "MergeScene1",
        "MergeScene2",
        "MergeScene3",
    };

	Promise.All(
			scenesToLoad.Select(sceneName => 
				AdditiveSceneLoader.LoadScene(sceneName)
			)
		)
        .Then(() => Debug.Log("Loaded all scenes."))
        .Catch(ex => Debug.LogException(ex, this));
 
Note that in the `Promise.All` version all scenes are loaded simultaneously. If what you really wanted was to load them sequentially, that's when you need to use LINQ `Aggregate`.

# Conclusion

In this short article I've explained various code examples that demonstrate how to express Unity asynchronous operations as promises. The code is available [online](https://github.com/Real-Serious-Games/Unity-Async-and-Promises).  
