---
layout: post
title: Threads, promises and Unity
date: '2017-06-19 23:11:04'
---

Are you interested in using threads with Unity? Wondering what promises, if anything, have to do with threads? In this post I answer an interesting question from Morgan Moon of [Cerebus Interactive](https://www.cerberusinteractive.com/) about the [C# promise library](https://github.com/Real-Serious-Games/C-Sharp-Promise), threads and Unity. I've been wanting to talk about threads for a while, so let's get into it.

# Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
*generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Morgan's question](#morgansquestion)
- [The really short answer](#thereallyshortanswer)
- [What is multi-threading?](#whatismultithreading)
- [Multi-threading gets complicated](#multithreadinggetscomplicated)
- [Using threads in Unity](#usingthreadsinunity)
- [ThreadPool](#threadpool)
- [Coroutines vs Threads](#coroutinesvsthreads)
- [Managing a thread with a promise](#managingathreadwithapromise)
- [Example Unity project](#exampleunityproject)
- [Conclusion](#conclusion)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# Morgan's question

> I have recently started working with your C-Sharp-Promise library for Unity, and it's been great. I am however wondering if this library deals with threading so I can run synchronous code as a promise, or is this something I'd have to implement myself? Or is this where you would wrap it with a unity coroutine in a promise? I am trying to avoid unity coroutines at all costs ;)

# The really short answer

The [C-Sharp promises](https://github.com/Real-Serious-Games/C-Sharp-Promise) library doesn't contain any support for threading and doesn't really have anything to do with threading, but you can easily use the promise library with threads. Just instantiate a promise, pass it into your thread function then call `Resolve` on the promise when the thread has completed it's work. Also don't forget to call `Reject` if anything goes wrong, that allows you to chain your error handling.

Threads and co-routines are different, which I'll explain later in this post, and it's difficult and unecessary to avoid coroutines in Unity, so maybe don't try too hard to do that! Co-routines are ugly but they can be [managed with promises](http://www.what-could-possibly-go-wrong.com/using-promises-for-unity-async-operations/) and unlike worker threads, co-routines can access the Unity API without having to jump through hoops.

Please read on for code examples and a longer discussion about threads, promises and Unity...

# What is multi-threading?

Creating a multi-threaded application is the process of splitting your application up so that you may have multiple streams of code execution. When developing a game you might split rendering, physics and game logic into separate threads. 

Why do this? Because you can achieve better performance if your game executes theses separate tasks in [parallel](https://en.wikipedia.org/wiki/Parallel_computing). This means each thread can potentially run on a separate CPU core and when that happens you really can get massive performance improvements. Even when there is only a single CPU core you still can gain some performance benefits, say if one thread is [blocked doing IO](https://stackoverflow.com/questions/1241429/blocking-io-vs-non-blocking-io-looking-for-good-articles) the other threads can continue to do their respective jobs.

Of course whether or not you can increase performance through multi-threading depends a lot on the situation at hand and on your architecture. You can't just throw multi-threading into the mix and expect it to work: you are going to need an understanding of how it can help and if it fits your problem.

Separate threads are typically used in games for loading assets, so that while the game continues to run, render and be interactive, its assets are being loaded in the background. [Open world games](https://en.wikipedia.org/wiki/Open_world) especially make use of this kind of technique.

# Multi-threading gets complicated

There is a big problem with threading: it can complicate things massively. 

Starting a thread and managing its lifetime is not too difficult. Having two threads in a application is very manageable, say you have the main thread and then one worker thread for loading assets. 

Still there are a series of potential problems you must deal with and they almost always center on how threads are synchronized and how they share data.

Problems that can occur when adding even one extra thread:

- [Race conditions](https://en.wikipedia.org/wiki/Race_condition)
- [Deadlocks](https://en.wikipedia.org/wiki/Deadlock)
- [Livelocks](https://en.wikipedia.org/wiki/Deadlock#Livelock)
- [False sharing](https://en.wikipedia.org/wiki/False_sharing)
- Shared data corruption
- Timing based issues
- Non-determinstic code execution: unrepeatable sequences of thread interaction

These problems all contribute to complexity in debugging, maintenance nightmares and weird bugs that only show for users and can never again be reproduced.

So you need to be very very careful about how you share data between your threads and how you synchronize them.

Have I turned you off threads yet?

It gets worse. Have you heard of [Metcalf's Law](https://en.wikipedia.org/wiki/Metcalfe%27s_law)? I've mentioned it before in this blog and I think it applies to multi-threading as well. As you add more communicating threads to your game the complexity of the communication between them becomes exponentially worse. As a rule, the more threads you have the more difficult it is to debug your code and understand what the hell it is doing.

**So don't go getting into multi-threading lightly or because it sounds cool**. 

It's an advanced programming technique and you can easily get bogged down in thread-related problems. Needless to say this isn't good for your project. Please make sure you weigh up the costs and benefits.

When you have multiple threads running and working on the same data at the same time it can be difficult to avoid corrupting your data. If possible you should only allow a single thread at a time to work on a block of data. If you need multiple threads to work on the data at the same time you'll have to use locking to prevent the threads from smashing the data and locking can cause it's own problems, for example deadlocks. 

An alternative to locking is to use [lockfree data structures](http://www.boyet.com/Articles/LockfreeStack.html) to manage your data. Don't try and roll your own though. If [Jon Skeet can't do it](https://stackoverflow.com/a/154582/25868) you most certainly can't. Lockfree data structures are fantastic, presuming you have robust and bulletproof code to handle this. If you are interested then [Julian Bucknall's blog](http://www.boyet.com/Articles/LockfreeStack.html) is a good place to start.

# Using threads in Unity

So you want to use threads with Unity? 

Normally in a game engine we'd use different threads for rendering, effects, physics, etc. But Unity takes care of most of that for us. Also, rather unfortunately we can only access the Unity API from the main thread, this means we can't use the Unity API from worker threads. This is a big limitation! However threads are still useful in Unity for loading data and doing rare and expensive computations.

Eventually you will probably need to join a worker thread back to the main thread in order to do something with the data that was loaded or the expensive calculation that was performed. You can't use the Unity API from a worker thread, so any subsequent work that is to be done with the Unity API must be [dispatched to run on the main thread](http://www.what-could-possibly-go-wrong.com/the-dispatcher-pattern/).

Starting a worker thread is really simple. First you need a *thread function* that will be run within the worker thread:

    private void ThreadFn(object threadInput) 
    {
        //
        // Do work in the thread.
        //

        // ...
    }

To start the worker thread: instantiate a [`Thread`](https://msdn.microsoft.com/en-us/library/system.threading.thread(v=vs.110).aspx) and call `Start` on it:

    var thread = new Thread(ThreadFn);
    var threadInput = ...; // Some input object to pass to the thread.
    thread.Start(threadInput);

You'll need to import the `System.Threading` namespace to compile this code.

# ThreadPool

When you have a bunch of threads to manage it might be easier for you to use the [`ThreadPool`](https://msdn.microsoft.com/en-us/library/system.threading.threadpool(v=vs.110).aspx). Again you need a thread function, but this time you don't need to explictly create a thead, just queue your thread function to be executed on the next available thread in the thread pool:

    var threadInput = ...; // Some input object to pass to the thread.
    ThreadPool.QueueUserWorkItem(ThreadFn, threadInput);

Using a thread pool can be a little more efficient as threads can be reused multiple times, so there there is no overhead next time to create the thread, if there is one available it will be reused.

# Coroutines vs Threads

So what do coroutines have to do with threads? 

Well, nothing really. Coroutines are not threads. A coroutine might seem like it is a thread, but coroutines execute within the main thread. 

The difference between a coroutine and a thread is very much like the difference between [cooperative multitasking](https://en.wikipedia.org/wiki/Cooperative_multitasking) and [preemptive multitasking](https://en.wikipedia.org/wiki/Preemption_(computing)). Note that a coroutine runs on the main thread and must voluntarily yield control back to it, if control is not yielded (this is where the coroutine must be *cooperative*) then your coroutine will [hang](https://en.wikipedia.org/wiki/Hang_(computing)) your main thread, thus hanging your game. 

Because coroutines run in the main thread you won't have any of the added complexity that you get when using multiple threads that must share data. That's a good argument for sticking with coroutines if you can. Unfortunately the things that threads are good for don't necessarily apply to coroutines. If you try do any [blocking operations](https://en.wikipedia.org/wiki/Blocking_(computing)) in a coroutines you will block the main thread, so you should consider using worker threads in these scenarios.

# Managing a thread with a promise

Now let's look at how to manage a thread with a promise. We'll get straight into the detail. If you need an indepth introduction to promises, please see my [earlier article on the subject](http://www.what-could-possibly-go-wrong.com/promises-for-game-development/).

The following example code shows the basics of managing a thread with a promise:

    public class ExampleThreadStarter : MonoBehaviour 
    {
        //
        // The thread function: This function runs in the thread.
        //
        private void ThreadFn(object threadInput) 
        {
            var promise = (Promise)threadInput;

            //
            // Do work in the thread.
            //

            // ...

            //
            // Resolve the promise when the thread is complete.
            //
            promise.Resolve();
        }

        //
        // Call this function to start the thread.
        //
        public IPromise StartWorkerThread() 
        {
            var thread = new Thread(ThreadFn);
            var promise = new Promise();
            thread.Start(promise);
            return promise;
        }
    }

To retrieve a result back from the thread, some kind of output from the thread, you must use the [generic](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/index) version of `Promise`. For example say you want to get a string back, you need to instantiate your promise as follows:

    var promise = new Promise<string>();

Now pass the string-resolving promise into your thread function. When the work is done resolve the promise with an appropriate value:

        private void ThreadFn(object threadInput) 
        {
            var promise = (IPendingPromise<string>)threadInput;

            //
            // Do work in the thread.
            //

            // ...

            //
            // Resolve the promise with an appropriate 
            // value when the thread is complete.
            //
            promise.Resolve("Hi from inside the thread!");
        }

Also be sure to call `Reject` on your promise if anything goes wrong. If you don't do this you will silently lose errors - you won't know that any problem has happened. It's best to put an exception handler around your thread function so you can reject the promise if *anything* goes wrong:

        private void ThreadFn(object threadInput) 
        {
            var promise = (IPendingPromise<string>)threadInput;

            try
            {
                //
                // Do work in the thread.
                //

                //
                // Resolve the promise when the thread is complete.
                //
                promise.Resolve("Hi from inside the thread!");
            }
            catch (Exception ex) 
            {
                promise.Reject(ex);                
            }
        }

Now you can start your thread and wait for the promise to resolve or reject:

    var aGameObject = ...
    var threadStarter =    
        aGameObject.GetComponent<ExampleThreadStarter>();
    threadStarter.StartWorkerThread()
        .Then(result => {
            Debug.Log("Thread completed, result is: " + result);
        })
        .Catch(ex => {
            Debug.Log("An error occurred in the thread!");
        })

There's nothing more to it than that!

# Example Unity project

Working example code and support is [available for my Patrons](https://www.patreon.com/whatcouldpossiblygowrong). The example project demonstrates use of `Thread`, `ThreadPool`, `Promise` and `Dispatcher`.

# Conclusion

My advice: Only use threads when you really need to. Then restrict the number of threads to something that you can manage.

Working with a large number of threads is difficult and fraught with problems. Your product will be much more likely to exhibit seemingly random and unreproduceable bugs.

Don't avoid coroutines, they are a necessary evil when working with Unity. Do wrap your coroutines in promises, they will make your life a bit easier.

# Resources

C-Sharp-Promise library: https://github.com/Real-Serious-Games/C-Sharp-Promise