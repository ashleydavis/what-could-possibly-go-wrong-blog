---
layout: post
title: 'Bringing MVVM to Unity - part 1: About MVVM and Unity-Weld'
image: "/content/images/2017/02/Main.png"
date: '2017-02-06 06:07:25'
permalink: bringing-mvvm-to-unity-part-1-about-mvvm-and-unity-weld
---

Do you need a better way to structure your Unity UI and reduce complexity? Are you looking for a cohesive way to coordinate the multiple parts of your UI? 

Let me introduce Unity-Weld: an open source implementation of the MVVM design pattern that will help you to structure your Unity UI.

Unity-Weld is the glue between the Unity hierarchy and the code that implements your UI logic. It binds together the parts of your UI: it coordinates them and allows them to communicate. Best of all Unity-Weld is simple: simple to learn and simple to use. It works well with Unity and feels like it fits with Unity's UI system.

In this series of articles I show you how to use Unity-Weld to take advantage of MVVM in your own Unity UIs. 

In part 1 I cover some background, explaining what MVVM is and why you should use it.

[Unity-Weld is available on github](https://github.com/Real-Serious-Games/Unity-Weld).

# Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
*Generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Do I need to know MVVM to use Unity-Weld?](#doineedtoknowaboutmvvmtouseunityweld)
- [So what is MVVM?](#sowhatismvvm)
- [Why MVVM?](#whymvvm)
- [MVVM for Unity](#mvvmforunity)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Do I need to know about MVVM to use Unity-Weld?

No you don't need to have used MVVM before to take advantage of Unity-Weld. I'll explain everything you need to know in this series of articles.

If you already have some experience with MVVM then you'll be able to skip or skim some of the sections in part 1.

# So what is MVVM?

[MVVM or model-view-view-model](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel) is a [design pattern](https://en.wikipedia.org/wiki/Design_pattern) and a method of structuring your UI that promotes a clean organisation and helps us achieve [unit-testing](https://en.wikipedia.org/wiki/Unit_testing). I'd argue that MVVM, in the context of a Unity application, really helps us tie together and organise all the disparate scripts that are normally scattered throughout the hierarchy. It's a way of formalising and structuring the communication that happens between components, something that can otherwise easily evolve in a haphazard and inconsistent way.

MVVM nicely [separates](https://en.wikipedia.org/wiki/Separation_of_concerns) your UI rendering (*the view*) from your UI logic (*the view-model*):

![](/content/images/2017/02/MVVM.png)

It is this separation that allows for the code isolation that you need for automated testing of your UI logic.

MVVM also allows for separate views on the same UI logic, simply by wiring multiple views to a single view-model instance:

![](/content/images/2017/02/MVVM_4.png)

There are two main aspects of MVVM. The first is [data-binding](https://en.wikipedia.org/wiki/Data_binding) which forms a connection between properties  of your view-model and your view. Changes on either side of the connection automatically propagate to the other:

![](/content/images/2017/02/MVVM_2.png)

The other aspect of MVVM is [event-based programming](https://en.wikipedia.org/wiki/Event-driven_programming), where the view raises events (triggered by user action) that are then handled by the view-model:

![](/content/images/2017/02/MVVM_3.png)

MVVM is commonly associated with [Windows Presentation Foundation (or WPF)](https://en.wikipedia.org/wiki/WPF) and that might be how you have heard of it. However the design pattern itself has roots going back to the [presentation model pattern](http://martinfowler.com/eaaDev/PresentationModel.html) and the
 [MVC pattern](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). 

The MVC pattern has many [variants](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller#See_also). So is it correct that I describe Unity-Weld as an implementation of MVVM? Well I have been inspired by and continue to be inspired by the MVVM pattern that underlies WPF. New patterns get created all the time as they are evolved to handle new situations and purposes. From my perspective Unity-Weld is a direct descendent of MVVM even though it differs in implementation from what has come before.

# Why MVVM?

It has been said that the primary purpose of MVVM is to enable test driven development and unit-testing for UI logic. Correct implementation of MVVM certainly makes this possible and I would even say straight-forward to create automated tests for UI logic, something that, in times past, has been considered difficult if not impossible. So TDD has been a major selling point for MVVM.

However I would argue other benefits, especially when it comes to working with Unity. So if TDD isn't your thing, I still think you can gain substantial benefits from using MVVM. For a start it brings about positive changes to your application design. It makes for an elegant structure with a good clean separation between UI rendering/control and UI logic.

Typically a Unity application is composed of a complex hierarchy with numerous scripts attached and scattered throughout. These scripts must communicate and work together to form a functional UI. MVVM can be used to consolidate and tie together your particular collection of scripts and form them into a cohesive whole.

As mentioned already, a beneficial side effect of MVVM is that you can have multiple views connected to a single view-model and automatically synchronized via MVVM. When you update the view-model, all connected views automatically stay updated.

# MVVM for Unity

Unity-Weld allows you to wire up your [view-model](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel#Components_of_MVVM_pattern) to your [view](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel#Components_of_MVVM_pattern) via the Unity hierarchy. Our view-models can be MonoBehaviours or they can be a pure C# classes. Our views are Unity components, such as `InputField` and `Button` that are added to the hierarchy. We must then add extra components such as `OneWayPropertyBinding`, `TwoWayPropertyBinding` and `EventBinding` that take care of the connections between views and view-models. Unity-Weld then automatically maintains synchronisation between view-model and view for us. The glue between the properties is what is commonly known as [data-binding](https://en.wikipedia.org/wiki/Data_binding). We have given the name *event-binding* to the glue between UI events and view-model functions.

When building applications with Unity we have one main mechanism at our disposal for structuring an application. That is Unity's implementation of the [entity component system pattern](https://en.wikipedia.org/wiki/Entity_component_system). Whilst that's a useful pattern we usually need more to structure a complex application. I've discussed [dependency injection](http://www.what-could-possibly-go-wrong.com/dependency-injection-for-unity-part-1) previously and that's one part of this puzzle. MVVM is another part. These patterns together help us to make sense of and relate the mess of scripts and game objects that make up your game. Dependency injection and MVVM create a structural backbone for your application on which you can hang all other features.

I believe that MVVM solves significant problems in Unity UI development and it makes Unity-UI development easier. However MVVM (and particularly WPF) has taken flak in the past for being difficult to learn. I've kept this in mind while developing Unity-Weld so that it is designed for ease of learning and fast and effective use. I hope that Unity-Weld can make the process of wiring up a UI more enjoyable, or at least that to make it cause less friction.

# Conclusion

In this first post of the Unity-Weld series, I've overviewed MVVM and I've introduced Unity-Weld, a library that brings MVVM to Unity and allows you to data-bind your UI to your view-model. Hopefully I've explained how useful MVVM can be for structuring your UI and convinced you that Unity-Weld can make your life easier.

In [*Part 2*](http://www.what-could-possibly-go-wrong.com/bringing-mvvm-to-unity-part-2-property-and-event-bindings/) I explain the basics of using Unity-Weld.