---
layout: post
title: Testing for Game Development
date: '2016-04-27 09:21:23'
permalink: testing-for-game-development/
disqus_id: ghost-15
---


> The single most important rule of testing is to do it.

Kerningham Pike 1999
 
Do you program games? Yes? Then this article is for you. It doesnÔÇÖt matter what language, engine, API or platform you use. It doesnÔÇÖt matter how many games youÔÇÖve made or plan on making or even the size of your games. This article is still for you. Because no matter what game you create, it will require testing. You would never hand over something youÔÇÖve made without giving it a go yourself, would you?

Slapping code together and putting it out there [cowboy style](https://en.wikipedia.org/wiki/Cowboy_coding) isnÔÇÖt exactly best practice. We call the cycle of changing code and chasing bugs *bug [whack-a-mole](https://en.wikipedia.org/wiki/Whac-A-Mole)*. This wasteful process leads to a slippery slope of programmer stress and increasingly poor decision making.
 
You need to know that the code you just wrote is likely to work before even committing to [version control](https://en.wikipedia.org/wiki/Version_control). More than that you need to know that you didnÔÇÖt just [break something else](https://en.wikipedia.org/wiki/Software_regression) that you had already tested.

In this article we cover:

- Our philosophy of testing.
 - Guiding development through testing, instead of the more common approach of having testing follow development. 
 - Understanding why test driven development is important.
- Process, techniques and technologies for testing.
- A brief look at testing in the Unity engine, since we use it at Real Serious Games.

This article is intended mostly for programmers, although the other disciplines can benefit as well. We aim to be as broad as possible and the knowledge here draws on many years of experience (and associated pain) in the games industry. We believe there will be something to take away for any level of developer.

**A note to QA**

This article is less about *quality assurance* and more about testing as an integrated part of the development process. Nothing we say here diminishes the need for a good QA department. We believe that a good QA department should work closely and be tightly integrated with the development team and actively involved in building the product.

# Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Outcomes](#outcomes)
- [The attributes of well tested software](#theattributesofwelltestedsoftware)
- [Philosophy](#philosophy)
- [Automation](#automation)
- [Process](#process)
- [Types of Testing](#typesoftesting)
- [Types of automated testing](#typesofautomatedtesting)
- [Techniques](#techniques)
- [Technology](#technology)
- [Unity](#unity)
- [Conclusion](#conclusion)
- [About the Authors](#abouttheauthors)
- [About the Reviewers](#aboutthereviewers)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Outcomes

What does testing give you? This question doesnÔÇÖt seem that difficult to answer. *Testing verifies that your software works, right?*

Yes. But we can go deeper than that and we can be more specific. Testing verifies that your software works as intended. More importantly it verifies that your software doesnÔÇÖt exhibit unintended consequences (for example formatting your hard drive, unless of course it is a format program!).

That's the main reason we *do* testing, however there are many other consequential side effects that improve our process, improve our product and help keep us sane. LetÔÇÖs look at some of these.

Testing helps us understand how our software *actually works*. This canÔÇÖt be understated. Have you ever returned to code you wrote 3 months ago? Do you still understand how it works? Add a team of developers, communication issues, complex requirements, changing scope and evolving design. 3 months later, do you still understand how it works? Large and complex software exhibits [emergent behavior](http://c2.com/cgi/wiki?EmergentBehavior) and is never as predictable as we would like it to be. This is especially true for games. 

With the combination of complex game logic and unpredictable player behaviour, are you confident your game will react gracefully in every situation? Testing generates knowledge and understanding of the product. This decreases uncertainty which in-turn reduces product risk. We are not just talking about the risk that the game will ship with hidden issues, although thatÔÇÖs certainly a problem that we have seen affect even the largest of the triple A games. We are also talking about the risk of building the *wrong thing*, a problem that is so prevalent in software development. Of course this can be harder to *pin-down* in game development, but only by playing and testing your game will you develop a better understanding of what the product is and where it should be heading.

Testing to a large extent can be automated. This requires considerable time and effort and can never fully replace the need for manual testing, however it can remove the bulk of *repetitive testing*. There is no denying that it can be difficult to achieve automated testing. But when you do, it becomes the scaffolding that keeps your project together and the rails on which it can move forward and remain stable in the face of repeated changes and ongoing evolution. Without automated testing, it is much more difficult to keep software working over time - especially when it is being touched by many hands and rapidly progressed. 

Testing can improve planning and design. Any effort made to *think* before coding vastly improves the odds of getting it right the first time (or at least much closer to the mark). Having a test plan helps you understand where you are heading. This helps you jettison the busy work, those tasks that take time but ultimately donÔÇÖt add value to the product. Culling unnecessary scope early and quickly is one of the most effective *productivity enhancements* you can make. Having a well defined concept, because you thought it through and articulated it as a test plan helps solidify and refine your vision. This forethought can help you identify flaws in the vision and can help you reject ideas that you decide arenÔÇÖt going to work. Where possible you should reject bad ideas at an early stage before you have shed blood for them.

# The attributes of well tested software

Before we get into the philosophy and techniques of testing, we should first look at the desired attributes of well tested software. It is from the need to meet these attributes that we start our testing journey. 

**Completeness**<br>
Does the code do everything it is meant to do?

**Correctness**<br>
Does the code work without error?

**Performance and Resource Usage**<br>
Does the code have acceptable performance and resource usage?

**Reliability/Stability**<br>
Does the code work reliably?
Does the code handle unexpected events and bad data gracefully?

**Fun (bonus quality just for Games)**<br>
Does it do all that and is the game still fun?

Now thatÔÇÖs a lot of boxes to tick, however we arenÔÇÖt aiming at a perfect score across the board. So much of software development is about the trade-offs, judgement calls and balancing acts required to finish the job and in many cases near enough has to be good enough to get over the line.   

As game developers it is the last attribute that is so often front and center in our minds. While most of the techniques weÔÇÖll discuss here are useless for testing the *fun quality* of your game (putting it in players hands is always your best option for that), we can guarantee that not meeting any of these other qualities *will* have a detrimental effect on the fun, because a game that you canÔÇÖt play due to bugs or poor performance is by definition *not fun*.

# Philosophy

Guiding the development process by testing (aka [*test driven development* or *TDD*](https://en.wikipedia.org/wiki/Test-driven_development)) is a major part of our philosophy but we arenÔÇÖt just talking about unit tests. Any effort made to think about, plan or engage in test activities before coding will have a positive impact on development and we consider that to be a form of test driven development, although it might be closer to what other developers normally consider to be *[behaviour driven development](https://en.wikipedia.org/wiki/Behavior-driven_development)*. 

Your first exposure to TDD will likely have been when writing [unit-tests](http://martinfowler.com/bliki/UnitTest.html) in the *[test first](http://www.extremeprogramming.org/rules/testfirst.html)* manner. We started with TDD and unit-testing although we have since realised that TDD can also be applied without unit-tests and without any kind of [automation](https://en.wikipedia.org/wiki/Test_automation) (although automated testing is extremely beneficial and usually the end goal of TDD).

We aim to be *outcome orientated*. What is the outcome? What are we aiming at? How do we measure our success? Does the outcome provide value that outweighs the cost of its development? These are questions you should answer. You will most likely develop the answers over time as you come to understand your project. Test driven development requires an understanding of your direction and a focus on the outcome. You must know your direction and be able to plan before you start. If you donÔÇÖt know this, then take some time out, hack together a prototype to test your ideas, then come back to the test driven process.

We aim to have a *quality mindset*. This means we are aiming at a high quality outcome. How do we define quality? That canÔÇÖt be addressed here, it means different things to different people in different situations for different projects. We do however promote a *defect first* development process. That is to say that we prefer, where possible, to fix bugs before adding features. We attack *[technical debt](https://en.wikipedia.org/wiki/Technical_debt)* head on to maintain stability over the life of the project. Mounting defects equates to mounting risk that a project *wonÔÇÖt ship on time*. Minimizing defects and technical debt helps us maintain a rapid pace of change and a low cost for experimentation. Testing helps build a quality-focused mindset and promotes a high-quality product. This might be stating the obvious, but it has to be said that building a high quality product that works as intended, with minimal unanticipated issues is the goal of testing.

Our philosophy means that we attempt to define our direction and outcomes up front. Of course we donÔÇÖt always get that right, but at least we always know where we are *supposed to be heading*. We must be vigilant and constantly monitor our heading. We must be ready to change course to remain competitive and be able to build the best game possible. There are times when we must take a step back, re-evaluate and replan as necessary.

So how does test driven development help us achieve our *attributes of well tested software*? Through our philosophy and process we can...

**Define acceptable**<br>
What is the acceptable level of quality for each of the attributes? That depends on the target hardware, audience and game genre. Test driven development forces you to plan ahead. You have to know what to test after all. 

**Identify when we arenÔÇÖt meeting acceptable**<br>
Every time a feature is added or modified, youÔÇÖll know the impact of it by looking at the output of testing. When something goes wrong we have a record of what was added and how it impacted the game.

**Estimate impact of unplanned scope changes**<br>
YouÔÇÖre in the middle of a meeting and someone comes up with a new idea. All heads turn to you and a voice rings out. *Can we do it?* Luckily, you have your recent metrics in hand and you can provide a reasonably educated guess in response.

Two of our most important principles are *keep it working* and *testing is not a phase*. Repeat these to yourself. Put them on the wall next to your monitor.  If, in the attempt to move the project forward, perhaps at a pace youÔÇÖre happy with or perhaps at a pace that someone else is demanding, you cannot keep to these two mantras, it means youÔÇÖre losing control of your process. YouÔÇÖll lose the ability to keep a rapid pace and your ability to estimate tasks will be diminished.

Ultimately the main takeaway about test driven development is that planning before coding is *always* a good idea.

# Automation

Automation is so useful in game development. It will take long, drawn out and error prone processes and make them fast, repeatable, reliable and has the potential to free up your time for the things that really matter. This extra time can help you concentrate on making a better game.

Building automation, however, is expensive and time consuming. In the long term it pays for itself, but you have to make sure it doesnÔÇÖt distract too much from actually making your game in the first place. After all you must finish that game to ensure that you do actually survive for the long term. 

You will need to make the trade-off as to how much time to invest in automation. The benefits are clear, but the cost can be high. The cost of automation should be amortized over the lifetime of your project. Evolve your automation system in time-boxed increments, that will prevent you spending too much time on it. Over time you will build out your toolbox of techniques and tools that can be carried over to the next project and the one after that. Like we said, itÔÇÖs a longer term investment. Over time automation requires less work and the benefits will eventually massively outweigh the costs when your projects are kept on the rails by your automated testing framework. The moment someone commits a change that breaks your build or existing functionality, you will be sent a notification. A system like this makes it much easier to manage *technical debt*.

How do you get started? Start with automating your build process and creating a *continuous integration* system (more on that later). Maybe then automate other tasks that you do 3 or more times. DonÔÇÖt worry too much about how this worksÔÇª just make sure that each task is *worth* automating and that 80% or more of the workload is automated. ThatÔÇÖs going to save you so much time. Remember that automation can be expensive, the time it takes to automate something should vastly outweigh the cost of doing that task manually, if it doesnÔÇÖt, then itÔÇÖs just not worth automating.

Build up to writing automated tests. *Unit tests* take a lot of skill, work and care and have a large impact on the design of the code (a good impact, but time consuming to get right). Consider saving unit tests for your most important, difficult or troublesome code. *Integration tests* are easier to implement and they give more bang for buck. Integration tests also have less impact on the design of the code, this makes it easier to build integration tests for existing code. *Smoke tests* are run against the final build and are the easiest by far so itÔÇÖs worth starting with them. More on these types of testing below.

# Process

When we develop software we work within a process. If you are new to development then you are probably still creating your process. If you have been doing this for some time you may still be creating your process. We say this because the software development process is an ever evolving system. It must change to fit the changing needs of the company, the team and the project. Always be on the lookout for improvements to the process, including dropping parts of it because they no longer deliver value.

Testing must fit within our development process. It should not be considered a separate phase or the responsibility of a different department. Testing is an intrinsic part of development and inextricably linked to it. Remember that *testing is not a phase* and it is tightly bound to the development process. Testing happens before, during and after development. The whole process should be iterative and [agile](https://en.wikipedia.org/wiki/Agile_software_development) with feedback from the previous iteration influencing the current iteration and so forth.

The general process of test driven development is to *set expectations, write code and add product features, then verify that the code and features meet the expectations that were originally set*. When doing TDD with unit tests the *set expectations* part involves writing *actual* code. The alternative to TDD of course is the traditional development method: *write product code, manually test that it works, repeat*. 

The key to TDD is understanding your outcomes and setting expectations before coding. If you are writing automated tests then your expectations will be expressed in code. If you arenÔÇÖt doing this, for whatever reason, write your expectations down in English: this is commonly called a *[test plan](https://en.wikipedia.org/wiki/Test_plan)*. Whether you write automated tests or just write down your expectations on paper is less important, the point is that you understand what you are trying to achieve and you have a plan to verify against at *[code complete](https://en.wikipedia.org/wiki/Software_release_life_cycle#Release_candidate)*. Or course TDD combined with unit tests and automation means that you can continue to run our tests automatically into the future, thus ensuring that your code is never *accidentally* broken, something that seems to happen quite frequently without automation and gets worse as more developers are added to the team and especially so for code that is rapidly evolving to meet the changing needs of the product.

Before we look at the flow of a single [iteration](https://en.wikipedia.org/wiki/Iterative_and_incremental_development), a caveat: Different companies and teams have different needs, so donÔÇÖt be afraid to take this, change it and make it work for you. DonÔÇÖt be afraid to throw out existing systems when you need to forge your own path. Take the best of whatÔÇÖs out there and build your own process, perfectly suited to your own needs. 

While you are reading through this please donÔÇÖt assume that we mean that an iteration will be a perfectly segregated [*waterfall*](https://en.wikipedia.org/wiki/Waterfall_model). Software development is a chaotic and messy business and it is rarely this simple, but we do need to show how testing fits the process so letÔÇÖs see how.

With that said, a typical iteration of development could be broken down into the following steps:

**1. Prototype**

Optional. This is unnecessary if you already know what you are doing well enough to start planning. To figure out where you are heading you should prototype. The key here is *minimal effort*. Know what the important questions you need answered are. Prototype only enough to answer those questions. Purposefully work quickly, cut corners and donÔÇÖt worry about testing. Make sure the work is [time-boxed](https://en.wikipedia.org/wiki/Timeboxing). Do not polish the work. Prototypes that are polished and *pretty much* do the job often end up going into production. Sometimes this works out ok, other times the shoddy workmanship of the prototype will bite you many times before the end of the project.

**2. Product planning and design**

Plan the next increment of your product. This is like *sprint planning* if you are doing [Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)). Focus on the results. What is your major goal? What are you going to deliver? What value does it bring? Why is it important? 

Code design can be part of this as well, but donÔÇÖt focus too much on it here. We promote design through development, that is to say that technical planning and code design, like testing, should be done directly before coding. We feel that these activities are an intrinsic and ongoing part of the development process.

**3. Test planning**

Plan how you will test the results of this increment. Plan the tools you will need for testing. Do you have the tools already? Do you have a plan for developing the tools you need?

**4. Automation**

Spend some dedicated time building tools to automate your testing process. Also plan time for learning about and improving on your test driven development skills. Time-box these activities. A small amount of time spent every iteration on improving your *development infrastructure* adds up and will pay dividends for future iterations. ItÔÇÖs worthwhile *amortizing* the cost of building and improving development infrastructure over the life of the project. This is quite expensive and it could take months of development if you did it all at once, so build out your infrastructure a bit at a time.

**5. Development**

Development is made up of the following deeply intertwined activities: Design, coding and testing. It doesnÔÇÖt feel right to consider them separately. The *development part* of the iteration, the major part of course, is the daily cycle of design, technical planning, coding and testing. This is repeated until the iterations goals have been met. ItÔÇÖs very important in this phase to have a tight feedback loop. Test driven development will keep you focused on the outcomes.

**6. Exploration**

In each iteration reserve time just to *explore* the product, looking for issues. Many problems are found this way. We coders are biased, we often write tests for the way the *code was supposed to work* or for the problems that we expect to find. So often, though, the real issues are caused by players doing the unexpected. This is one area where your QA department will excel. TheyÔÇÖll do the things you didnÔÇÖt expect and find the issues you didnÔÇÖt anticipate.

**7. Review**

Review the results of the iteration. Were the goals met? Does the game meet the standards set by our testing plan? What issues got in our way? How do we improve on testing in the next iteration? This is the time to re-evaluate your plan and your direction and think of the process improvements that will make you more effective and efficient in the next iteration. If you are doing Scrum this is a *sprint review*.

# Types of Testing

**Manual/repetitive testing**

Manual testing is the process of manually following a test plan to test software for defects . This isnÔÇÖt difficult and it isnÔÇÖt automated. ItÔÇÖs easy and inexpensive to start this way, however the costs mount over time as in session after session you repeat the exact same process. Over time you should seek to automate this form of testing.

**Exploratory testing**

Reserve time to to *explore* your game as opposed to running mechanically through your test plan. You will find problems that wouldnÔÇÖt otherwise have been found.

**Focus testing**

Put your game in front of a sample of the target audience. They will use it in ways you didnÔÇÖt expect and find bugs of which you wonÔÇÖt have dreamt.

**Soak testing**

Run your game overnight. Run your game for days at a time. Having some level of automation really helps make this *possible*. YouÔÇÖll probably want to be able to script a playthrough of your game or use the *demo* or *attract* mode if you have have of those. Make sure you output a log of frame rate and memory usage. Graphing performance metrics allows you to check if performance is getting worse over the duration of the test.

# Types of automated testing

**Unit Tests**

Units of code are tested in [isolation](http://agileinaflash.blogspot.com.au/2012/04/is-your-unit-test-isolated.html). [Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection) and [mocking](https://en.wikipedia.org/wiki/Mock_object) simulate connections to other code modules. Unit testing is expensive to build, although it becomes less so with better tools and more experience. Unit testing is worthwhile at least for your most difficult and complex code, the code that needs to be rock-solid reliable. Designing code for unit testing results in code that is very loosely coupled, so working this way will have a positive effect on the design of your system.

**Integration Tests**

This is similar to unit testing except usually a number of code modules or a significant part of the system are tested together as one. The boundary of the code under test is larger than that for unit-testing. Unit-tests usually test a single class, integration tests work with a larger number of collaborating classes.

Integration testing is much more like testing the final (integrated) system because (usually) no parts of the system are simulated. It is far less expensive than unit testing because the design restrictions imposed by the isolation are lifted, meaning you can hack out tightly coupled code to your heartÔÇÖs content. Your code doesnÔÇÖt have to be so well designedÔÇª itÔÇÖs a trade-off you might make to get something done quickly. 

*Designing for disposability* is something thatÔÇÖs very interesting to us at the moment. This is where you design parts of your system (think *components* or *[microservices](https://en.wikipedia.org/wiki/Microservices)*) to eventually be thrown out. The whole system, the integration of the parts, is designed in such a way that the parts can be thrown out and replaced with little impact or damage to the system as a whole. The system continues to function and over time we replace entire sections of it. Use TDD and integration testing to build the parts of your system. DonÔÇÖt worry too much about good design within the *part*, you are planning to throw it away someday, so *hacking* is ok.

**Smoke Tests**

Smoke tests usually test the entire complete system (or at least some major part of it). This is what we like to refer to as *full build testing*, in that the tests are going to be run against your *entire* game. This is the least expensive form of testing but it can be difficult to get started with. You need to be able to script or replay input to your game somehow. You also need to be able to determine what the game is doing while being tested, fortunately this part isnÔÇÖt difficult if you already have decent logging and metrics output from your game. This output can then be checked by the testing framework to verify the game is doing what it is supposed to be doing. This kind of testing can be extensive or it can just scratch the surface, any level of smoke testing will have a massive impact on keeping your game working over time.

# Techniques

There are many techniques for testing. This section is a summary of techniques we have found useful.

**Play your game**

This seems obvious, but it is the place to start with testing. Play your game, *frequently*.

**Test the actual build**

For convenice and fast turn-around you will test builds that you create on your local workstation. However, a feature shouldn't be considered complete until it has been tested in a proper build of the game. DonÔÇÖt just test it in the Unity Editor and donÔÇÖt just test it in a development build. Make sure it actually works in a build after it has been integrated with code and assets from the whole team. 

If you have a *build script* and/or *continuous integration* system, your final testing should be done on the build that comes out of that.

**Test plan**

Have a written test plan. What will you test? What is the anticipated behaviour? At what frequency will you test? How will you record the outcomes of testing sessions? These are questions you should answer. Having a process will help you stay sane.

**Shorten the feedback cycle**

Make sure that the round-trip between making a change and being able to test it is as short as possible. This ensures that testing is fast and efficient and can be performed frequently. Making testing easier increases the likelihood that you will be disciplined and that testing will actually get done. Having a fast, convenient and automated build process helps. What you really need though are customized levels for testing. For testing specific features you want a cut down level that loads very quickly, has the game camera in the right position and has the right player and relevant props and NPCs conveniently instantiated near each other. Anything you can do to minimize the round trip from change, to seeing the effect of the change will make you more *productive*.

**Resource and performance budgets**

What levels of performance and resources usage are you aiming at? You should have an understanding of the performance characteristics you are trying to achieve. What is the desired frame rate, memory usage, poly count, number of entities on screen, etc? Write this down otherwise you will forget it. Tests like this are fairly easily automated.

**Keep your documents updated**

All the documents we talk about should be considered live. Keep them updated as you develop your understanding of your game, your audience and as you improve your process. Although try not to be document heavy! Having a working a game is better than having a complete set of documents, but having zero documentation isnÔÇÖt great either.

**Test driven development**

Drive your development through testing. This is the most important technique and is integral to our philosophy of testing so we have discussed it at some length already. Create unit tests if you can. Otherwise create integration tests, they are easier to code and maintain than unit tests but are also very effective. If you donÔÇÖt have the time or capability to code automated tests consider just using the principles of TDD to guide your development as we have already discussed. 

**Output Testing**

Outputting useful data from a *test* build of your game is one of the cheapest methods of testing and translates very well to automated testing. This is simply called *logging*. If you are logging to a human readable text file you can visually inspect the output to verify that game logic is behaving as expected. 

You can take this much further still. Commit previous *log* files to your *version control* system and you can now easily *diff* the outputs to quickly learn if behaviour has changed. 

You can go further againÔÇª put your game on autopilot (feeding input events in) and record output. Have your build system email you if detects a change in the output that has been logged. This is one of the cheapest methods of automated testing that we know of and yet is still very effective.

**Automation**

If you write automated tests make sure they are scheduled to run frequently. Any decent *continuous integration* system will allow you to create builds and run tests either periodically or on events (such as on *version control* commit).

Make your game scriptable. This isnÔÇÖt as hard as you might think. Make it able to load a sequence of levels and replace the normal player camera with an AI camera that follows a scripted path through scene. Or record player inputs and play them back to simulate game play.

**Graphical Testing**

If you have a completely deterministic renderer you can compare screen captures between different runs of the build. A program like [*Image Magick* compare](http://www.imagemagick.org/script/compare.php) actually makes this easy. 

Making your rendering deterministic is a different story. YouÔÇÖll need to be pretty dedicated if you want to make this work.

**Determinism**

This isnÔÇÖt as  much a technique as it is something for which you should strive. Having a deterministic game (or at least as deterministic as it needs to be) is very important for testing and also many other aspects of game development. Determinism is the ability to have predictable outcomes from the game when the inputs are the same. Put another way: the game should respond the same way under the exact same conditions. 

If you donÔÇÖt have some level of determinism then how do you expect to reproduce issues that have been reported by players or QA? Test driven development helps improve determinism. You also need a solid random number generator (more on that soon). 

We have written much more on determinism than can fit in this article, so please stay tuned for a future article on the topic.

# Technology

Here is a summary of the tools, tech and features we have used to make testing more efficient and effective.

**Build process**

Your [build process](https://en.wikipedia.org/wiki/Software_build) should be automated and be 100% reliable and repeatable. Make sure everyone on the team can make a build easily and with minimal setup. You should be able to run a build from the command line, this will make it easier to automate your builds through *continuous integration*. 

Make sure the development team all understand the build process and the tools it is built on. It is *fundamental* that developers have an understanding of how their tools work. They must be equipped to modify and improve their tools so they have control over improving their own workflow. The best way to achieve this is to have every developer work on the build script at some point.

The build process should easily work both in the CI system and on local developer workstations, otherwise how else would you *test* and *debug* the build script? It is so important that the build process itself be **extremely well tested**! Builds and release are traditionally fraught with errors. Automate your build and practice it frequently and you will reduce the risk that builds will be broken because of the build process.

**Test framework and associated libraries**

For unit testing and automated integration testing you need a good test framework and test runner. YouÔÇÖll probably want to be able to run unit tests from the command line (so they can be included in your automated build process). You should also be able to run unit tests within your chosen game engine. For this you may have to code your own test runner, but itÔÇÖs worth it as some issues may only show up when running under the game engine.

At Real Serious Games we use [XUnit.net](https://en.wikipedia.org/wiki/XUnit.net), but it is just one of many that are available for every [conceivable programming language](https://en.wikipedia.org/wiki/List_of_unit_testing_frameworks). For proper isolation in unit-testing youÔÇÖll need to use *dependency injection*. Then you can use a *mocking* library to replace your dependencies. We use [Moq](https://github.com/Moq/moq4/).

**Version control system**

If you <del>are a professional developer</del> know what's good for you then you are already using [version control](https://en.wikipedia.org/wiki/Version_control). End of story. Version control is so important to the software development process that itÔÇÖs difficult to do it just justice in this small amount of space. 

You should commit only small changes. This will make it easier to track down the revision where a bug first occurred. Bug investigation can be significantly easier when you can quickly track down the change that caused a bug. This will also tell you which developer made the change. Speak to that developer to understand the intention and reasoning behind the change. This all serves to get you much closer to a fix for the bug. 

If your version control system has a [bisect feature](https://en.wikipedia.org/wiki/Bisection_(software_engineering)) then use that to find the breaking revision very quickly. Once you start to use bisect you will find that it will influence your coding practices, you will start to code so that it is easier to bisect.

At Real Serious Games we use either [Git](https://en.wikipedia.org/wiki/Git_(software)), [Mercurial](https://en.wikipedia.org/wiki/Mercurial) or [Perforce](https://en.wikipedia.org/wiki/Perforce) depending on the project, the team and the circumstances.

**Bug tracking system**

Use a bug tracking system to log and track the status of your issues. We currently use [Redmine](https://en.wikipedia.org/wiki/Redmine), a free open-source solution. If possible you should make things easier for yourself and use a single system for both task and issue tracking. Our reasoning is that fixing bugs should be given a status on par with adding features, that is to say that both should be prioritized according to necessity and the value that they deliver to the game.

**Diff tool**

You need a way to visually check differences between files. You will need to check the differences between code revisions, to find or understand the revision where a bug was introduced. You also may want to diff output logs as mentioned earlier in *Output Testing*. 

We use [WinMerge](http://winmerge.org/) and [Beyond Compare](http://www.scootersoftware.com/), but there are many other good tools available. 

**Debug tools**

Your game should log all important events. This is great for testing, you can visually inspect the log to verify that the game logic is working as expected. Better yet you should record your logging (eg to a text file or database). This will allow you to inspect what happened *after the fact*, for example you will be able to investigate *after* a player has experienced a bug. Logging is great for higher level automated testing of the following form... 

- Script your game (as mentioned earlier) 
- Use code to scan the logs to test that some event has occurred.

Your game should output performance metrics. You can log these to a file, for example log directly to a [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) file and your metrics will already be in a convenient format to load into Excel for analysis and graphing! Even more sophisticated: record your metrics to a database that you can later query and use for data analysis (at this point we are obliged to promote our open source data analysis toolkit [data-forge](https://github.com/data-forge/data-forge-js)). Using a database means you can build a web app to show the results. Or better yet make use of something like [Influxdb](https://influxdata.com/time-series-platform/influxdb/) and [Grafana](http://grafana.org/) to record and visualize your performance data. 

Spend time thinking about and improving the debugging and visualization systems in your game. You should easily be able to enable and disable entire systems. When searching for an issue use debug features like this will help you quickly narrow down the problem space. In a virtual world you should be able to visualize the things (like force vectors) that you canÔÇÖt normally see. YouÔÇÖll be amazed at some of the bugs that are so easily solved just because you have seen the problem through your debug visualization system.

**Automation tools**

You should buy or build your own tools for event recording and playback. You can record input events, so that a playerÔÇÖs input may be played back to simulate their game. You can record network events to simulate interaction with a network player or the server.

Your game should be scriptable to some degree. This could be as simple as having command line options that allow jumping to a particular level and then initiate event playback or drive a camera along a pre-recorded path. More sophisticated: your game could respond to network commands (delivered via HTTP or sockets) that instruct it to start a level or initiate event playback.

**Continuous integration system**

Automation tools are good on their own. They can help accelerate the process on your own workstation. However they really come into their own when builds and tests are run automatically in a continuous integration system. We use [Jenkins](https://jenkins.io/index.html) which is free and something of a standard in the software industry. There are many other options that you might want to consider. 

There are three main ways to make use of your CI system:

- It builds the game and runs tests whenever someone commits changes to the version control system. If a developer breaks the build or tests the team is automatically notified. This can automate a significant part of your testing process and provides immediate feedback when the game is broken.
- It builds and runs tests on some schedule. This is appropriate when the game is large and the build process takes considerable time. You still want to use the first option to run unit tests or other simple fast tests whenever someone commits, but if the full build process is long then you may only want to do a full build and test once per day.
- It allows your colleagues to self-service build and test. The team can serve themselves and request a build and test at any time. This is very useful when you have many projects or a complex build process... as we do at Real Serious Games.... and itÔÇÖs not practical to have them all building on every commit or on a daily schedule.

Your CI system will keep records or your past builds, including the history of build stability (eg the number of automated test failures). This information can be invaluable when you need to understand the history of a project.

**Random Number Generator**

One of the easiest ways to have determinism in your game is to control the seeds to your [random number generator](https://en.wikipedia.org/wiki/Random_number_generation). ItÔÇÖs important that different systems (eg traffic, pedestrians, etc) have separate isolated random number generators. This helps a lot to make these individual system predicable. It means that you can isolate particular system from the rest of the game and still have them behave the same way, reproducibility is very useful when investigating issues in these systems.

# Unity

LetÔÇÖs briefly discuss the testing tech we use with Unity at Real Serious Games. 

**Build Process**

At Real Serious Games our build process has gone through multiple stages of evolution. It started life as a batch file. When it got more complicated we tried [Python](https://en.wikipedia.org/wiki/Python_(programming_language)). After we started building web applications we naturally wanted to use JavaScript and [Grunt](http://gruntjs.com/) as our build system. As time progressed the build script became more and more complex as our builds required more complex preparation and automated testing. Grunt was not enough to manage this complexity (many inter-dependent build tasks). We tried [Gulp](http://gulpjs.com/) as well which is much better but also didnÔÇÖt meet our needs. 

So we created and open sourced [Task-Mule](https://www.npmjs.com/package/task-mule), our own custom automation system that can handle the complex and interdependent web of build tasks that we have now.

Our CI system and our developers run the exact same build script. It runs a [headless invocation of Unity](http://docs.unity3d.com/Manual/CommandLineArguments.html) to build the project.  

**Unity Log**

The *Unity log* is your friend. It is the first place you should check for errors when investigating an issue. 

The *build log* is also useful and youÔÇÖll want to wire this into your build process and CI system. 

If you experience a crash in a build, the log can save you some time tracking down the issue. If the Editor crashes, this can be even more likely. Finding the Editor log file can be tricky. The paths for the various platforms, both editor and build logs, can be found here: [http://docs.unity3d.com/Manual/LogFiles.html](http://docs.unity3d.com/Manual/LogFiles.html).

**Dumping System Info**

Log system info to the filesystem. System info can be retrieved through UnityÔÇÖs [SystemInfo class](http://docs.unity3d.com/ScriptReference/SystemInfo.html). If you publish on the PC you can make use of the [DxDiag command](https://en.wikipedia.org/wiki/DxDiag) to understand a userÔÇÖs system. Sometimes you will have issues that manifest on particular platforms, systems or hardware. In these cases it is invaluable to understand what system your users are running.

**Unit Testing**

These days Unity has a testing framework that is [available on the asset store](https://www.assetstore.unity3d.com/en/#!/content/13802) for versions of Unity 4.0 and higher. In version 5.3, the Unity test runner comes built into the Unity Editor. This looks really useful, but when we started using Unity, this wasnÔÇÖt available. We use [xUnit](https://xunit.github.io/) to write our tests and [Moq](https://github.com/Moq/moq4) for mocking. We use the [RSG Factory](https://github.com/Real-Serious-Games/Factory) for dependency injection. 

We run our test under the XUnit test runner in Visual Studio. We also have a hand-coded test runner to run our tests under Unity. Why is this useful? It means we can verify that our code works under the normal .NET framework and also under UnityÔÇÖs Mono framework. Sometimes the differences between the two can cause problems. ItÔÇÖs fast and convenient to run your unit tests in Visual Studio, but this is no guarantee that the same code will under Mono, so please test both.

With Moq we can create *[mock](https://en.wikipedia.org/wiki/Mock_object)* objects to simulate dependencies for unit testing. This obviously presents problems when dealing with Unity classes that donÔÇÖt have interfaces or virtual functions. We use a simple abstraction layer to make it possible to mock the Unity API, although this gives a layer of indirection that isnÔÇÖt ideal and we are investigating alternatives that we might write about in a future article.

**Continuous Integration**

We use [Jenkins](https://jenkins-ci.org/) for our continuous integration services. Jenkins monitors the code repository (usually [Mercurial](https://en.wikipedia.org/wiki/Mercurial)), and performs a build and runs tests when there have been changes to the repository.

Our Unity projects (usually [Perforce](https://en.wikipedia.org/wiki/Perforce)) are also built through Jenkins. These builds are usually triggered by self-service, although some of our builds are also on a nightly schedule.

**Logging and Telemetry**

Logging is a valuable and surprisingly simple tool for testing. Unity has a built in [logging API](http://docs.unity3d.com/ScriptReference/Debug.Log.html) that will get you started, but itÔÇÖs really not that difficult to roll your own system that is so much more flexible and powerful. Our logging system is based on the .NET 3.5 port of [Serilog](http://serilog.net/). 

We have also written various supporting tools to help us monitor live logging and query stored logging. The [LogServer](https://github.com/real-serious-games/logserver) is a tiny node.js server that accepts logs via HTTP and stores them in a [MongoDb database](https://en.wikipedia.org/wiki/MongoDB). The [LogViewer](https://github.com/real-serious-games/logviewer) reads the logs from the database and displays them in a web page. With this system we can very conveniently aggregate log streams from multiple builds, different devices and even different platforms into a single searchable system.

Our [metrics system](https://github.com/real-serious-games/metrics) is similar to our logging system. We can output events and data to [server](https://github.com/Real-Serious-Games/Metrics-Server) where they are stored in a database. This is invaluable for understanding our frame rate and other metrics over time. We can measure device temperature, memory allocation, player position & direction and any other values that can be packed into a [JSON](https://en.wikipedia.org/wiki/JSON) payload. 

Because logs and metrics are stored in a database we are able to query and inspect data from previous application instances. It is so powerful to be able to query a database for information after an issue has been logged. We can go back in time and analyze what happened during that playerÔÇÖs session. The fact that this system operates remotely means we can understand our users and their issues wherever they might be. Time and distance are no longer the barrier that they used to be.

If you are interested in having Serilog for Unity please check out the .NET 3.5 branch of [AshÔÇÖs fork](https://github.com/ashleydavis/serilog/tree/feature-net35-backport). Please be warned though that getting code like this to work under Unity is a stunt that should only be performed by trained professionals, donÔÇÖt try this at home kids.

**JSON serialization**

YouÔÇÖll probably need a good JSON serializer for your general serialization needs. Having a JSON serializer is also useful for capturing events (for later playback) and *output testing* that was mentioned earlier. Unity 5.3 (finally) comes with [its own JSON serializer](http://docs.unity3d.com/Manual/JSONSerialization.html) that weÔÇÖd like to try out soon. To date we have used JSON.NET. ItÔÇÖs fantastic but can be difficult to get working under Unity. You can find a [fork that works under Unity here](https://github.com/codecapers/Unity.Newtonsoft.Json), although we have heard this doesnÔÇÖt work under iOS.

**Random Number Generation**

You can set the [seed](http://docs.unity3d.com/ScriptReference/Random-seed.html) for [UnityÔÇÖs random number generator](http://docs.unity3d.com/ScriptReference/Random.html). This is a good start for having a determinstic game. Unfortunately though this system is global and so a single stream of random numbers must be shared out to all of your systems. This can lead to systems that interfere with each other and cause unpredictable behaviour. That means you can't isolate your systems effectively (for example you canÔÇÖt pull out your *pedestrians* system and have it behave the same way on its own). 

At Real Serious Games we use [SimpleRNG](http://www.codeproject.com/Articles/25172/Simple-Random-Number-Generation) to generate our random numbers.

# Conclusion

In this article we have covered software testing as applied to game development. This is really just *software testing* tailored as we see fit to the concerns of game developers. 

You have learned about our philosophy of testing and some of the techniques and technologies that can make testing work for you. Although the testing techniques discussed here can be used with any language or game engine, we have shown how some of them apply to Unity.

We hope you are now in a better position to test and can appreciate the value of testing. At the very least we will have made you think more about it. In the best case you can now flip around your entire dev process and drive it forward with testing front and center.

If you take nothing else away from this article, remember these things:



- Testing can't be left to the end. Doing so delays and accumulates risk to the end of the project where it can do the most damage.
- Testing will help you deliver a high-quality game that works as intended, has no major issues and works within performance constraints.
- Test driven development can (and probably should) guide your development and vastly improve your process (and we arenÔÇÖt just talking about unit tests!).

And finally... *just do some testing*.

# About the Authors

## Ashley Davis

Ash is an experienced software developer and has been developing games professionally since 1998 with a few interludes in other industries. Ash now builds serious games, simulations and VR experiences at [Real Serious Games](http://www.realseriousgames.com/). Ash is also a contractor under the name [Code Capers](http://www.codecapers.com.au/) and has built products that span multiple platforms. 

Ash contributes to the open source community and is a founder and organiser of [Game Technology Brisbane](http://www.meetup.com/Game-Technology-Brisbane/) and [Game Development Brisbane](http://www.meetup.com/Game-development-Brisbane/). Ash also helps out organising [Brisbane Unity Developers](http://www.meetup.com/brisbane-unity-developers/).

For a longer bio please see Ash's profile on [linked in](https://www.linkedin.com/in/ashleydavis75).

## Adam Single

Adam is a passionate game developer. Adam is ex-RSG and now a senior member of the Well Placed Cactus development team. He's an organiser for Game Technology Brisbane, Game Development Brisbane and Brisbane Unity Developer meetups. 

He's also the co-founder of a small, two man team called OneCoin Creations. As fathers, they fit the development of their own games in between work and family life. 

For more info please see: [https://branded.me/adamsingle](https://branded.me/adamsingle)

# About the Reviewers

A big thank you to our reviewers who have helped make this article what it is.

## Mark Hogben

Mark is a professional software engineer with over sixteen years experience, most of which has been in the games industry. He is currently working on his own game projects, and writing his second novel.

## Leigh Mannes

Leigh is the technical lead for Well Placed Cactus. He joined the team after more than a decade working across mobile, online and real-world experiential installations for agencies and institutions around the world.

[http://bearstoxicchemicals.com/](http://bearstoxicchemicals.com/)