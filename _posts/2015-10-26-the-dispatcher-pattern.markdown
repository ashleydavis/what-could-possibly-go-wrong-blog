---
layout: post
title: The Dispatcher Pattern
date: '2015-10-26 08:12:31'
---

The dispatcher pattern is a method for scheduling (or *dispatching*) code for execution from a worker-[thread](https://en.wikipedia.org/wiki/Thread_(computing)) to the main-thread.

Actually... this pattern can be used to schedule code to any single thread, however it is most commonly used with the main-thread.

It is quite simple to use. At [Real Serious Games](http://www.realseriousgames.com/) we have been using the dispatcher pattern with [Unity3d](https://en.wikipedia.org/wiki/Unity_(game_engine)) for a few years now. I first learned about this pattern when I was coding UI on the [Windows Presentation Foundation (WPF)](https://en.wikipedia.org/wiki/Windows_Presentation_Foundation). 

In this short article I'll show you why you might want to use this pattern and how to implement it for Unity3d.

## Motivation

So you have some code that you need to execute on the main-thread. For example, you have a callback from an asynchronorous or background-thread operation and the response can only talk to an API on the main-thread.

**An example from UI programming:**

You have a time-consuming background operation happening in a worker-thread. During the operation you need to update a progress bar, after the operation you must close the progress bar. To achieve both of these you must execute the UI-update code on the main-thread (this is often the case when working with UI APIs such as WPF).

**An example from Unity3d programming:**

The above example can also apply to Unity3d programming, but I'll come up with something new instead. 

Imagine you have a stream of network commands incoming to your system (for example in a networked game) that is processed on a background worker-thread. Responses to some commands will hit the Unity3d API and this can only be done on the main-thread (otherwise Unity3d will throw an exception). So in response to the network command you must schedule some code to the -main-thread.

## How it is used

The dispatcher pattern is simple enough to achieve in C#, although it does require the use of [an anonymous function](https://msdn.microsoft.com/en-us/library/bb882516.aspx?f=255&MSPPError=-2147217396) which is a more advanced concept in C#.

Let's consider the *network command* example from the previous section.

The example code responds to an *update position* network command and modifies the [transform](http://docs.unity3d.com/ScriptReference/Transform.html) of the current [GameObject](http://docs.unity3d.com/ScriptReference/GameObject.html). This is *how we would like* it to work:

	//
	// Handler that is invoked in a background-thread.
	//
	void HandleNetworkCommand(Command incomingCommand) 
	{
		switch (incomingCommand.Type) 
		{
			...
		
			case NetworkCommandType.UpdatePosition:
				this.transform.position = 
					((PositionCommand)incomingCommand).newPosition;
				break;

			...
		}
	}

Unfortunately this won't work. The network command handler runs on a worker-thread. Maybe this is to reduce the impact on the rendering-thread (and thus on our framerate) or maybe it just a limitation imposed by the networking library. Whichever, Unity3d will not allow us to interact with its API except from the main-thread.

So we must rework the code to use the dispatcher pattern: 


	IDispatcher dispatcher = ...;

	void HandleNetworkCommand(Command incomingCommand) 
	{
		switch (incomingCommand.Type) 
		{
			...
		
			case NetworkCommandType.UpdatePosition:
				dispatcher.Invoke(() =>
				{
					this.transform.position = 
						((PositionCommand)incomingCommand).newPosition;
				});
				break;

			...
		}
	}

Now we have some code that is packaged in a [C# anonymous function](https://msdn.microsoft.com/en-us/library/bb882516.aspx?f=255&MSPPError=-2147217396) and passed to the dispatcher to be later *invoked* on the main-thread:

	dispatcher.Invoke(() =>
	{
		// work to be done on the main-thread
	});

At some point in the future (in our case this will be next [update](http://gameprogrammingpatterns.com/update-method.html)) the code will be executed in the main-thread. 

## Implementation under Unity3d

The implementation of dispatcher for Unity3d is surprisingly simple. First we need an interface that we can use to schedule code as an anonymous function:

	public interface IDispatcher
	{	
		void Invoke(Action fn);
	}

The [Action](https://msdn.microsoft.com/en-us/library/system.action(v=vs.110).aspx) class allows us to take a reference to an anonymous function and store it for later execution:	

	public class Dispatcher : IDispatcher
	{
		public List<Action> pending = new List<Action>();

		//
		// Schedule code for execution in the main-thread.
		//
		public void Invoke(Action fn)
		{
			pending.Add(fn);
		}
	} 

We also need a function that can invoke our *pending actions*:  

	public class Dispatcher : IDispatcher
	{
		...

		//
		// Execute pending actions.
		//
		public void InvokePending()
		{
			foreach (var action in pending)
			{
				action(); // Invoke the action.
			}

			pending.Clear(); // Clear the pending list.
		}
	} 

Of course, the dispatcher is designed for intra-thread communication, so it must be [thread-safe](https://en.wikipedia.org/wiki/Thread_safety)! 

This is easily achieved by [locking](https://msdn.microsoft.com/en-us/library/c5kehkcz.aspx?f=255&MSPPError=-2147217396) on the *pending* list: 

	public class Dispatcher : IDispatcher
	{
		...

		public void Invoke(Action fn)
		{
			lock (pending)
			{
				pending.Add(fn);
			}
		}

		public void InvokePending()
		{
			lock (pending)
			{
				foreach (var action in pending)
				{
					action();
				}
	
				pending.Clear();
			}
		}
	} 

The question now is how do we update the dispatcher?

This leads to another important question: how do we access the dispatcher? 

We need to access the dispatcher to update it. We also need to access it to schedule code to the main-thread.

To keep things simple for this article I'll make the dispatcher a [singleton](http://gameprogrammingpatterns.com/singleton.html), although for larger or more complex projects I recommend the use of the *dependency injection pattern* (which I intend to write about in a future article).

	public class Dispatcher : IDispatcher
	{
		private static Dispatcher instance;

		public static Dispatcher Instance 
		{
			get 
			{
				if (instance == null)
				{
					// Instance singleton on first use.
					instance = new Dispatcher(); 
				}

   			 return instance;
 			}
		} 
	

		... rest of the code ...
	} 

So now we can access the dispatcher via its *Instance* property:

	Dispatcher.Instance.Invoke(
		() => SomethingThatMustRunInTheMainThread()
	);

We can now return to the first question and implement a [MonoBehaviour](http://docs.unity3d.com/ScriptReference/MonoBehaviour.html) that executes our scheduled code:

	public class DispatcherUpdate : MonoBehaviour
	{
		void Update()
		{
			Dispatcher.Instance.InvokePending();
		}
	}

This MonoBehaviour should be attached to a single GameObject in the scene. Don't forget to add it somewhere, or your scheduled code will never be executed. 

This works because the [Update method](http://docs.unity3d.com/ScriptReference/MonoBehaviour.Update.html) of MonoBehaviour is automatically called by Unity3d on the main-thread. It invokes our *dispatched* code, therefore that code will run on the main-thread, right where we want it.

## Conclusion

Job done. We can now schedule code for execution on the main-thread.

Ok, so the dispatcher pattern is far from perfect. It kind of gets in the way and clutters up our nice clean code. 

However it is practical and simple and it is very much unit-testable (we have done this many times).

## Further considerations

Here are some other things that you find interesting to look into:

- Using *dependency injection* instead of *singleton* to get the *IDispatcher* dependency into the objects that require it.
- Using a [lock-free queue](https://www.google.com.au/webhp?sourceid=chrome-instant&ion=1&espv=2&es_th=1&ie=UTF-8#q=c%23%20lock%20free%20queues&es_th=1) for better performance instead of [the lock statement](https://msdn.microsoft.com/en-us/library/c5kehkcz.aspx?f=255&MSPPError=-2147217396) (don't roll your own, it's way too difficult to get right).
- Look at [scheduling and threading](http://www.introtorx.com/Content/v1.0.10621.0/15_SchedulingAndThreading.html#SchedulingAndThreading) in [Rx](http://www.introtorx.com/). I've had Rx working under Unity3d and it's great if you can represent your system as reactive response to data streams (eg streams of network commands).

