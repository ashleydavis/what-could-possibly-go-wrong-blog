---
layout: post
title: 'Dependency injection  for Unity - Part 1: About dependency injection'
date: '2016-10-31 20:26:00'
permalink: dependency-injection-for-unity-part-1
disqus_id: ghost-24
---

Have you ever struggled to keep a game or application working as it evolves and as the complexity ramps up? Software is composed of a suite of interacting components that are wired together in a particular way. As the number of interacting components grows the wiring and the number of connections between components grows exponentially (a phenomenon known as [Metcalfe's law](https://en.wikipedia.org/wiki/Metcalfe%27s_law)). 

![](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1d/Metcalfe-Network-Effect.svg/220px-Metcalfe-Network-Effect.svg.png)

How can we keep a handle on this complexity and carve-out some order from the chaos?

You should consider using [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection), a technique for automatically wiring together complex applications. This sounds like it might be what we need, however dependency injection can itself be complicated and difficult to understand. Try out any of the dependency injection frameworks for C# and you can be forgiven for thinking that dependency injection was some kind of rocket science. 

In this series of articles I aim to explain dependency injection in simple terms. I'll try to convince you that dependency injection will help you manage complex applications. With working examples I'll show you how to use dependency injection with Unity. 

In this first part to the series I explain dependency injection and its benefits.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
# Contents  
*Generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [How does dependency injection fit in?](#howdoesdependencyinjectionfitin)
- [Why use dependency injection?](#whyusedependencyinjection)
- [What is dependency injection and how does it work?](#whatisdependencyinjectionandhowdoesitwork)
- [What are the benefits of dependency injection?](#whatarethebenefitsofdependencyinjection)
- [What are the downsides?](#whatarethedownsides)
- [Dependency injection and singletons](#dependencyinjectionandsingletons)
- [Dependency injection for Unity](#dependencyinjectionforunity)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# How does dependency injection fit in?

We have a problem to solve: How do we remove the manual connection of components that costs time and effort to setup and maintain?

We are aiming for connections that are automatic and that can survive as we restructure the scene or refactor our code. How do we this? I'm yet to even explain what dependency injection is, but I thought I'd start with this small example to give a taste of where we are heading. 

This example shows how an `NPC` might automatically be connected to the `Player` object: 

	public class NPC : MonoBehaviour 
	{
		[Inject(InjectFrom.Anywhere)]
		public Player player; 

		void Start()
		{
			...
		}
	}

Note the use of the `Inject` attribute. This tells our dependency injection system to find a `Player` object anywhere in the scene and insert it into our `NPC` script. Dependency injection will do this for us automatically. The player is effectively a singleton, but the NPC doesn't care as long as it gets the player object *somehow* from *somewhere*.

We can also inject multiple objects by using an array property: 

	public class Vehicle : MonoBehaviour 
	{
		[Inject(InjectFrom.Anywhere)]
		public Pedestrian[] pedestrians; 

		void Start()
		{
			...
		}
	}

This gives you an idea of how dependency injection can work in Unity. I'll come back to this and explain it properly later in the series. For now let's take a closer look at dependency injection and the benefits it can bring.

# Why use dependency injection?

Dependency injection is a [design pattern](https://en.wikipedia.org/wiki/Design_pattern) and an implementation of the [*inversion of control*](https://en.wikipedia.org/wiki/Inversion_of_control) design principle. It helps simplify and automate the wiring of components in complex applications. It helps achieve [component isolation](http://c2.com/cgi/wiki?UnitTestIsolation), something that is important for [unit-testing](http://martinfowler.com/bliki/UnitTest.html).

# What is dependency injection and how does it work?

The following quote will help you understand the concept.

Dependency injection for five-year-olds:

> When you go and get things out of the refrigerator for yourself, you can cause problems. You might leave the door open, you might get something Mommy or Daddy doesn't want you to have. You might even be looking for something we don't even have or which has expired.
> 
> What you should be doing is stating a need, "I need something to drink with lunch," and then we will make sure you have something when you sit down to eat.
>
> John Munsch, 28 October 2009.

Source: [Dependency Injection on Wikipedia](https://en.wikipedia.org/wiki/Dependency_injection).

Let's relate this to code. You have a software component. It *declares* its needs to the dependency injection system. These are the services that it depends upon. These are its links to other components. You might also say that this is how it connects to the *outside world*. The component doesn't have to care where it's dependencies come from, only that they are somehow provided for it. This is the essence of dependency injection.   
 
![](/content/images/2016/10/Software_component.png)

The main point is that components are not explicitly wired together. Explicit wiring is ok for small and simple apps, but as your application grows more complex you will find that more problems manifest themselves in the wiring between components. You'll understand what I mean if you have ever had the experience where you change initialisation order and it breaks something. So you tweak that and then something else breaks. Managing initialisation order is painful and becomes more so as the number of connections between components increases. Having implicit and automated wiring makes it much easy to setup your system. It's also easy to rewire your system as things change. 

With dependency injection you don't manually wire up connections between components, this is done for you by a dedicated service that I like to call the *dependency resolver*.

# What are the benefits of dependency injection?

This is my take on the benefits of using dependency injection:

- The *concerns* of dependency-use and dependency-resolution are separated. [Separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) is a good thing.
- Software components are wired together auto-magically, you will spend much less time being concerned with system wiring and order of initialisation issues. For example it is easy to create new objects in a system that have convenient access to the range of services provided within the system.
- It reduces the occurrence of initialization order issues. For example, advanced systems can create dependencies on-demand so there is less need to care about what needs what and has that service been started yet.
- Dependency injection reduces hard-wiring in your application, this is one thing that causes your app to break when you decide to rearrange code, assets or the [Unity hierarchy](https://docs.unity3d.com/Manual/Hierarchy.html). Systems that are wired very tightly are also very fragile. This matters more as your applications grows and becomes more complex.
- Dependency injection encourages [less coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) between components. Striving for low coupling is an important software design principle.
- Dependency injection makes it easier to rewire your app as it evolves. Dependencies can be switched out for other dependencies that implement the same interface. The client component doesn't know or care, so long as it's dependency is satisfied *in some way*.
- Dependency injection makes [unit-testing](https://en.wikipedia.org/wiki/Unit_testing) and [test driven development](https://en.wikipedia.org/wiki/Test-driven_development) (TDD) easier. Injected dependencies can easily be replaced by [mock objects](https://en.wikipedia.org/wiki/Mock_object), this enables the code isolation required for pure unit-testing.
- Dependency injection can be used to centralise or externalize your system configuration, although personally I haven't yet used it in this way and I rarely use any explicit setup in my dependency injection systems. You might use a fluent API to configure your setup in code. Otherwise you might externalize your dependency setup through a configuration file. Imagine allowing your application wiring to be setup from a [json](https://en.wikipedia.org/wiki/JSON) file. It's debatable whether this is a good design practice, but I can imagine situations where it might be useful. 

# What are the downsides?

So there are plenty of amazing benefits, however I want to provides some balance... so what are the downsides of dependency injection?

I've been using dependency injection for several years and I can honestly only think of a single issue. Dependency injection reduces [coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) between your components. The way I use dependency injection makes the connections, and therefore the coupling, completely implicit. From a design and architecture perspective I think this is a good thing. 

From a static analysis point of view, it becomes more difficult to understand the connections that exist in the application. It becomes more difficult to understand how the app will be wired up at runtime. This creates a barrier to understanding application structure and it makes it difficult to mentally trace the flow of the application. I've heard this argument from others and I can understand where it's coming from. It *is* difficult to understand how complex applications are wired together.

However I'd argue that it can be difficult to grok the structure and flow of any complex application, regardless of whether you use dependency injection. The argument that it makes the application more difficult to understand only holds true for small applications. An application that grows and evolves over time into a more complex beast is by it's nature going to become more difficult to understand and this can be especially so for any traditional application. When every dependency must be wired manually (this in itself is a lot of work) it means that every dependency can be wired up differently. This is one thing that makes it so difficult to figure out, from one part of an application to another, what the [smeg](https://en.wikipedia.org/wiki/List_of_Red_Dwarf_concepts#Smeg) is going on. This is especially so in a team environment and even more so when the team's consistency is not up to scratch. 

Dependency injection actually helps more the larger the application becomes. If your dependency injection is automated (my dependency injection systems are almost 100% automated) then (in principle, at least) you always understand how the system will be wired up, it doesn't matter what part of the application you happen to be in, you know that it will be wired up consistently because it is an automated solution that is used and it is 100% consistent across the board. If you work this way then you won't get into the situation where different conventions are used in a different parts of the application, you use a single convention, you learn it once and that convention is enforced (through the automation) across the entire application. This makes your whole application vastly easier to comprehend.

# Dependency injection and singletons

Use of dependency injection has an important implication for our use of [singletons](https://en.wikipedia.org/wiki/Singleton_pattern). We no longer need explicit connections to singletons and this makes it possible to use them without referencing them via global variables. Normally singletons can be a problem in that they often seem to materialize a hard-wired and inelegant system structure and can make it very difficult to achieve isolation for unit-testing. However, for many other reasons, singletons are just so damn convenient! 

Dependency injection allows us to use singletons with less guilt, we can have the convenience of singletons without the problems. The software components themselves just request dependencies, they don't care if those dependencies happen to be singletons or otherwise. This makes it possible to unit-test components that use singletons, something that is tricky otherwise. This also gives you some flexibility to later remove singletons from your system as you evolve your [software architecture](https://en.wikipedia.org/wiki/Software_architecture) with minimal (or no) interruption to the classes that depended on the singletons.

# Dependency injection for Unity

I'll finish *Part 1* of this series by looking at the types of dependency injection we can make use of in Unity.

## Traditional factory-based dependency injection

Traditionally dependency injection makes use of the [factory pattern](https://en.wikipedia.org/wiki/Factory_(object-oriented_programming)). That is to say that a factory is used to create an [object](https://en.wikipedia.org/wiki/Object_(computer_science)) (not a game object, mind you, a normal C# object) and satisfy its dependencies as a combined operation. At Real Serious Games we created our own [factory that supports dependency injection](https://github.com/real-serious-games/factory). This is mature, well-tested and it works under Unity. We have used it to structure significant parts of our many Unity applications. This kind of factory supports [code isolation](http://c2.com/cgi/wiki?UnitTestIsolation) for [unit-testing](https://en.wikipedia.org/wiki/Unit_testing). That's the main reason it exists. When we unit-test a class, any objects that it creates through the factory can be *[mocked](https://en.wikipedia.org/wiki/Mock_object)*. Also any object created through the factory will have their dependencies automatically satisfied (or an error will be thrown if any particular dependency doesn't exist). This means that we can manually inject *mock* objects when we are testing a *factory-creatable* object.  

Whilst the *RSG Factory* works well in normal C# code it's not the simplest approach for dependency injection in Unity and it doesn't fit well with MonoBehaviors and the Unity hierarchy. This is possible and we have done it, but it works best when working with normal C# objects and feels clunky when working with MonoBehaviours. The problem is that MonoBehaviors aren't created in the same way as normal C# objects, they are attached to game objects via the Unity Editor and we never directly [*new up*](https://msdn.microsoft.com/en-us/library/fa0ab757.aspx) a MonoBehaviour (something that is common to regular C# programming), because of this we don't need, and indeed can't use, a factory to create MonoBehaviours. However we would like to automatically inject dependencies into our MonoBehaviours. So this is where my scene-based dependency injection technique comes into play... 

## Scene-based dependency injection

Scene-based dependency injection is an adaption of traditional dependency injection so that it fits better with Unity's main [architectural pattern](https://en.wikipedia.org/wiki/Architectural_pattern): *the hierarchy*. This version of the pattern keeps many of the benefits of traditional dependency injection: automated wiring and error checking. In addition and probably the most important outcome for many of my readers is that you can restructure your scene and refactor your code and your application will continue to work without any manual rewiring of components.   

Scene-based dependency injection, in a nutshell, connects together dependencies between MonoBehaviors that are instantiated in the Unity [hierarchy](https://docs.unity3d.com/Manual/Hierarchy.html). It is designed to be simple to understand, simple to use and plays nicely with Unity. Both scene-based and factory-based dependency injection can co-exist together in the same application. Use of one doesn't rule out use of the other. And use of either certainly doesn't rule out using as many manual connections as you care to use.  

Scene-based dependency injection means you can setup up the Unity hierarchy and your MonoBehaviors however you like. It scans your scene and automatically wires together your MonoBehaviors. It allows you to connect objects in the Unity hierarchy without having to manually use functions such as `FindObjectOfType(...)`. It can resolve the dependencies for the entire scene or just a sub-set of game objects, it is flexible according to your needs.

The actual wiring is all done automatically. [Attributes](https://msdn.microsoft.com/en-us/library/aa288454(v=vs.71).aspx) are used to to mark *injection points*. Dependencies are selected implicitly by their type. The system automatically reports errors so you can't forget your error checking. It is designed to cope well with configuration errors and to help you easily locate the source of the error.

# Conclusion

This concludes part 1 of my series on dependency injection in Unity. I briefly showed what we are aiming at: automatic connection of components in Unity. Then I went on to generally explain what dependency injection is and what it can do to help us structure and test a complex application.

Please stay tuned for [Part 2](http://www.what-could-possibly-go-wrong.com/dependency-injection-for-unity-part-2/?utm_source=ash&utm_medium=wcpgw-di-article-part-1&utm_campaign=dependency-injection2) where I discuss the kind of code that we are seeking to replace with dependency injection.
