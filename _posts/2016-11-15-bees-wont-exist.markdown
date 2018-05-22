---
layout: post
title: 'Guest post: The tech behind Bees Won''t Exist'
image: "/content/images/2016/11/screenshot-1.png"
date: '2016-11-15 03:00:46'
---

GIANT BEES HAVE DECIMATED HUMANITY! Every day, the low buzz of death approaches further. Can you hear it?

DonÔÇÖt worry! ItÔÇÖs time for some pest control. Grab your sword. IÔÇÖll meet you by the hive!

[Bees Won't Exist](http://gamejolt.com/games/bees-won-t-exist/194883) is a fast-paced hack 'n' slash adventure game that I have been working on over the past year at [Honeyvale games](http://honeyvalegames.com/). We are a team of 5; my role consists of Lead Programmer and Senior Exotic Tea Provider. Bees Won't Exist is our final year project at Queensland University of Technology and I'm posting to share some insight into the kinds of tech we used, what worked well, and what didn't.

# Tech used

## Unity 5.4
Early on we decided we wanted to make the game 3d so Unity was the obvious choice for an easy to use, full featured and affordable (free) 3d game engine. We did consider Unreal 4 and I'd still like to use that some time, but in the end we settled on Unity mostly because I was more familiar with it. 

While I've found Unity to be limiting for some certain kinds of projects it was definitely a good fit for Bees Won't Exist. In particular, the additive scene loading feature introduced in 5.3 was invaluable to us as it allowed us to take elements that were in every scene such as the player and the UI and put them in a separate "Shared" scene. This way, the other scenes only needed to contain objects specific to that level. This was good because it meant we didn't have to rely on a single prefab to contain everything, and we didn't need to remember to add the same objects to every single scene over and over again (which was a big deal because we ended up with a total of about 34 scenes in the project).

![Scene hierarchy](/content/images/2016/11/Scene-hierarchy.png)

Unity's API for writing custom editor extensions was also invaluable as it enabled us to write a full [hex-based tile editor](https://github.com/RoryDungan/HexTiles) that ran within the Unity scene editor. This was great for rapid greyboxing and helped us build the large areas of the game relatively quickly, without having to use any external tools.

## State Machines
The system used for managing player state uses [Fluent State Machine](https://github.com/Real-Serious-Games/Fluent-State-Machine), a [hierarchical finite state machine](http://aigamedev.com/open/article/hfsm-gist/) system I wrote at Real Serious Games. Hierarchical state machines have a big advantage over traditional "flat" state machines where each state is on the same level, because they allow you to create a stack of states. With a traditional approach you would always have to remember which state to go back to (i.e. stopping `running` will always go back to `idle`), but if `running` is just a child state of `idle` you can *pop* the top state off the stack to go back to its parent implicitly.

## Behaviour Trees
More complex AI for parts of the game like the two bosses was built using behaviour trees using the [fluent behaviour tree](http://www.what-could-possibly-go-wrong.com/fluent-behavior-trees-for-ai-and-game-logic/) library. You can read more about that library and behaviour trees in general in that article, but for us it ended up being quite useful for modelling complex behaviours and decision trees. 

## Achieving the look of the game - custom shaders
From the beginning of the project we wanted it to have a light, friendly look, reminiscent of children's cartoons. Unity's Standard Assets package inclues some "toon" shaders but we quickly found those limiting so didn't end up using them. The outlines they include don't really work on any objects with sharp edges, they don't allow any control over the material properties of the object like you get with Unity's standard PBR shader, and they only work in forward rendering mode. 

It took us quite a few iterations before we came up with a solution that really captured the look of the game we wanted but in the end I followed [this blog post](http://www.gamasutra.com/blogs/DavidLeon/20150702/247602/NextGen_Cel_Shading_in_Unity_5.php) and modified the Unity standard deferred shader to have a more cel-shaded look. This is done by quantising the light reflected off a surface to two flat steps. Because it's still using the standard PBR shader we can still have a fair level of detail using normal maps and can simulate a variety of materials using the shader's shininess and emmissive properties.  Using the standard deferred shader also gives us good performance with many lights and enables instancing, which ended up actually giving us better performance than the simpler forward rendering shader we had tried initially.

![Shaders](/content/images/2016/11/shaders.png)

The outlines are done using the "Edge detection" filter from Unity's Standard Assets. This takes the normals of everything in the scene and runs a sobel edge detection filter over them, and then combines that with another pass doing the same thing to the depth of all objects in the scene.  

## Online services

### Unity Cloud Build
From the start I really wanted some form of continuous integration set up and Unity Cloud Build was really easy to set up - just point it to a Git/Mercurial/Perforce repo and go! All the builds we published were made through Cloud Build and having a common source for the latest build, as well as an indicator of when the build was broken was a great asset. That said, towards the end of the project I did find myself really wishing we had a proper system like [Jenkins](https://jenkins.io/). Every time we needed to make a release I would have to run an [NSIS](http://nsis.sourceforge.net/Main_Page) script to build a Windows installer, package the Linux build as a .tar.bz2 archive, and copy the Mac build into a .dmg image. This took quite some time and involved rebooting *and* using multiple computers. The fact that there is no way to build something like this into Cloud Build as a post-build step, combined with the annoying "cooldown timer" it has that prevents you from creating builds quickly just makes me think that it's not really ready for any kind of serious use. 

Also I'm not sure if it's a problem with Cloud Build or Bitbucket who hosted our repo, but often Cloud Build would just fail while cloning our repo for no particular reason. We also had issues with builds coming out missing certain models for no particular reason until I disabled the option to cache the library folder between runs, although that must be a bug in Unity because I have seen it happen with a similar setup using Jenkins before.

### Bitbucket
We used Bitbucket for hosting our private Git repo and it was great. For code we had a policy that all new code would be created on a new branch and nothing would be merged until one other coder had reviewed it first. To facilitate this we made heavy use of Bitbucket's pull request/code review features and it all worked really well for us as a primarily remote team. 

The only real issue we had was that our repository did end up getting rather large and we started seeing warnings when it exceeded 1GB, although Bitbucket does support [git lfs](https://git-lfs.github.com/) so I'm sure we could have worked out a solution to this if it had ever been an issue. Also I mostly used Gitkraken as my Git client but have found it becomes very slow and sometimes just crashes when dealing with large files, so I can't recommend it for game development. Running the same commands from a terminal always worked though so I know the issues I was having weren't to do with Git itself.

![The most colourful git screenshot I could find](/content/images/2016/11/git.PNG)

Another alternative is GitHub, which would have probably been just as good.

### Jira
Our university mandated scrum development and we found Jira to be a great tool for managing this. Originally we just had Trello but the ability to prioritise issues, have backlogs of tasks, start sprints from collections of cards in the backlog and generate burndown reports easily made it quite useful. It does cost money and takes a lot of time to configure and set up but hey, if it's good enough for CD Projekt Red then it must all be worth it, right?

### Slack
Slack was great for us - I'd consider some sort of multiple channel-based messaging service a must-have for teams working remotely or even together. HipChat is another alternative from Atlassian (the makers of Bitbucket and Jira) that looks quite promising but I haven't used it.

We had a scrum meeting channel and did daily updates in the format of what we did since the last meeting, issues and our plans for the day, which was a great way to keep up to date.

### GameAnalytics
This one only really made it in because our university mandated that you must collect analytics from your game, but I guess it provided some interesting insights into who was playing it. I chose this over Unity Analytics purely because it didn't seem to have as many limits on how much data you can collect and it had error logging built-in, which you need to pay a lot of money for using Unity Analytics.

# What went well
Overall the game was quite successful for a student project. I find the game fun to play, which is a great achievement (and took us probably a bit over 6 months to reach) but also we were one of 6 teams from around Australia who got accepted into the [GCAP student showcase](http://gcap.com.au/gcap-student-showcase/).

One thing I think really helped during development was consistently working together in the same space. The uni has a great computer lab that we would hang out in at all kinds of ungodly hours and being able to talk to each other in person made things a lot easier.  

# Lessons learned
Even though we had 5 people, the scope of the game ended up becoming much bigger than I think any of us had planned. This isn't necessarily a bad thing itself, but it did lead to some pretty intense crunch time towards the end of the project. I think also because we had so much content and so many levels we didn't quite achieve the level of polish that everyone in the team wanted in all areas. I think the takeaway here is just to be careful when creating a content-based game, especially for something like a student project. 
![QA Testing](/content/images/2016/11/qa-testing.png)

Making editor extensions like our hex tile editor are great and *can* save a lot of time, but it's also easy as a programmer to get caught up in the tech and waste time making tools without actually making the game. We came way closer than I'd like to think about to just having to completely abandon the hex tile editor because it was taking me so long to write, and even after it was "finished" I ended up taking a lot of time away from the game to go back and fix issues with it.

I think next time I make a game I would like to focus on marketing much earlier in the project as well, since that got a bit crammed in at the end of the project. Having a proper devlog somewhere like Tigsource right from the beginning could have definitely helped get more people interested in the game and get feedback earlier on. 

# Final thoughts

Overall IÔÇÖm really happy with how Bees WonÔÇÖt Exist Turned out. Sure, there are many things I would do differently if I had a chance to do this again, but we learnt a lot from this project and I think everyone is pretty happy with the result.

ItÔÇÖs too early now to say what Honeyvale will be working on next (apart from bugfixes and patches for Bees WonÔÇÖt Exist), but weÔÇÖre definitely all going to keep making games so stay tuned!

# About the Author

Rory Dungan is a passionate programmer and a member of the Real Serious Games team. He has just finished studying games at QUT and so far has released two indie titles. Bees Won't Exist is the latest of these but certainly won't be the last.