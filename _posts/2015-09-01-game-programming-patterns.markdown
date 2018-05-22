---
layout: post
title: 'Game Programming Patterns: Book Review and Interview'
featured: true
date: '2015-09-01 09:41:05'
---

## Design Patterns

You've heard of [design patterns](https://en.wikipedia.org/wiki/Design_pattern) right? 

Before migrating to the software development industry design patterns were successful in traditional architecture. They hit the big-time through the 1994 software engineering classic [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns). Interestingly it wasn't just academic, the [Gang of Four](http://c2.com/cgi/wiki?GangOfFour) identified and documented patterns that were already in common use. The concept of design patterns gave us a new way to describe and design code. It provided a higher-level way to communicate about code and systems.  

Considering design patterns have been around for so long I actually find it surprising how few experienced developers actually understand design patterns (possibly excepting the ubiquitous singleton and factory). This appears even more so, in my opinion, in the case of game developers. 

[*Game Programming Patterns*](http://www.amazon.com/Game-Programming-Patterns-Robert-Nystrom/dp/0990582906) by Robert Nystrom looks set to increase awareness of design patterns in the game dev community. As anyone who works with me will testify, I'm an ardent fan of patterns and I really enjoyed Robert's book. It is filled with colorful, amusing and illustrative examples drawn from Robert's obviously broad experience as a game developer. 

Robert not only shows the spider web of connections between patterns, he also succeeds in connecting patterns to game development and demonstrating how useful they can be. This book has something for both new and experienced developers. The game dev community is growing rapidly, the ranks swelling with indie developers and hobbyists who can definitely learn much from this book.   

## Game Programming Patterns

In the beginning of the book, Robert covers some of the classic patterns whilst the remainder of the book is dedicated to numerous patterns that are more specific to game development: patterns for sequencing, behavior, decoupling and optimization. They range from high-level patterns that will help you structure and organize your gameplay code to some surprisingly low-level patterns.

I really enjoyed the low-level patterns. *Bytecode* was awesome (although I'm not sure how practical it is) and really took me back to my days inventing my own scripting languages for game dev. *Data Locality* was also great, an absorbing subject and a very difficult problem for low-level game devs. If you are a developer of small games these low-level patterns won't be as relevant for you, but it's still great having an understanding of the issues that face developers of larger games.

For each pattern Robert starts with the motivation for using the pattern and describes the pattern itself.  He then goes on to detail when to use it, how to approach it and what issues you may come up against. The book includes examples of each of the patterns and sample code that illustrate how the patterns fits into a game. Finally there is a discussion of the design issues and different ways of using the pattern. Robert is very frank about the trade-offs between concerns, the decisions that must be made and the issues that can arise depending on which fork in the road you take. I thought this was particularly insightful as development is all about judgement calls and making the right trade-offs at the right time. The final section for each pattern has a *See Also* section which is surprisingly useful for understanding the connections between patterns and their usage in the real world.

Of course no book like this could cover every pattern and Robert had to decide what to include and what to leave out. Unfortunately some of my favourite patterns including *dependency injection* and *inversion of control* were not included. There is a nod to dependency injection from the *Service Locator* pattern, but it doesn't get a chapter of it's own. I'm genuinely surprised that *factory* doesn't get it's own chapter, it's a basic pattern but I have seen it used a lot in game development. Why does *Singleton* have it's own chapter and not factory? I guess everyone has their own favourite patterns, but you need to be impartial if you write a book like this. 

The process by which Robert wrote this book is really interesting.  The book was written openly with much community support and a continuous feedback loop. The online version is still available to read free of charge. I was curious about what it was like to write a book in this way and so I reached out to Robert with some questions. He was kind enough to respond with some thoughtful answers. 

So this is where the book review transitions into an interview.

## Interview with Robert
   
*How long did it take you to write the book?*

About six years, though there was a two-year gap in the middle where I wasn't working on it.

*What inspired you to write the book?*

There's lots of reasons, big and small, but the main ones were: I was working in a lot of codebases that really could have benefited from it. Tons of really hard to understand and maintain spaghetti code.

I was looking to get a new job, and I thought it would be a nice thing to add to my r├®sum├®. (Ironically, I ended up getting a new job outside of the game industry before I finished the book. Oops!)

I'd always liked writing, and writing a book was something I wanted to do before I died. It was a book that I wished existed and no one else seemed to be writing it, so I figured I should. :)

*Did you learn a lot about design patterns through writing the book or did you already know much of it before you started writing?*

I knew the GoF patterns pretty well and most of the other patterns well enough to know that I wanted to include them. I had a list of chapters pretty much on day one and it didn't change much. But for many of the patterns, I had to do a lot of research to understand them well enough to feel comfortable writing about them. *Game Loop*, *Spatial Partition*, *Component*, and *Data Locality* in particular relied on a lot of research. Even for the patterns I (thought I) knew well, I did a bunch of reading beforehand.

*You have had many years in game development. When did you start learning design patterns?*

I remember clearly the evening a friend handed me his copy, but I'm trying to figure out when that evening was. I think somewhere around 1999-2000. I read most of it in one sitting. (I'm weird like that.)

*Much or all of the process was open and you wrote the book very publicly. Why did you do it this way?*

I'd like to say this was brilliant marketing on my part. But, really, it was to help me finish it. I'm really bad at finishing things. But, before I started the book, I had been blogging for a while. I noticed that the thought of getting people to read and comment on a blog post was just enough of a kick in the ass to get a post finished, and I thought the same would apply to book chapters.

Since my main goals with the book were about finishing a personal challenge, and raising my profile to find better work, doing it publicly made a lot of sense. I wasn't at all worried about losing sales by having the book online since money didn't factor into the equation in the first place. (Most technical books make very very little money.)

It turns out that I got very, very lucky. By putting the book out there for free, I got a ton of constructive feedback that I used to revise the text and make it much better. And it raised the profile of it so much that sales have been way better than I ever expected. I was hoping maybe it would make enough money to buy a few toys. Instead, it's going to put my kids through college.

*You didn't have much to say about one of my favorite design patterns (dependency injection).*

I think the Service Locator chapter mentions it. DI is one of those things that means different things to different people. In the original sense of "pass objects as arguments instead of creating them yourself", I think it's a useful way to think about organizing your code. In the newer sense of "use runtime metaprogramming to construct and wire up dependencies at runtime" like Guice, Angular, etc. I haven't seen it used in games.

*Do you get much feedback like this? Was anyone irritated that you left out their favorite pattern?*

Oh, yes. All the time. Especially when I was writing it, I would get email and bug reports like "How about adding a chapter on ___." And I was like, "I'm trying to actually reach the finish line here, not push it farther away!" If I ever do a second edition, I'll probably add a chapter or two.

In general, I found the hardest part about writing is deciding what to cut. The reader's attention is precious, so I tried to be careful with what I spent it on.

*Did you have any criteria about which design patterns to include or exclude?*

No hard criteria. It was a mixture of:

 - Am I personally excited about this pattern?
 - Is it something I don't think many people know about?
 - Do I think it would be helpful if they did?
 
One day I was driving home from work and started idly thinking about, "I wonder if I could write a book of design patterns for games. What ones would I do?" I started a mental list and when I got home, thought about it some more. About an hour later, I had most of the list figured out.

*If you wrote the book again are there any new patterns you'd include or patterns you'd leave out?*

I'm fairly happy with the list of patterns. I didn't change it much while writing even though I could have. Since each chapter is relatively independent, I had a lot of freedom to change the content over time. I didn't actually do that much because I was OK with the patterns in there.

There are some that aren't the stars of the book. I don't think *Update Method* or *Subclass Sandbox* are the most valuable chapters, but I think they are still worth having since they tie in to other things. The idea of components and entity component systems has evolved while I was writing the book, so I'd probably reorganize and possibly split *Component* into more than one chapter if I revise the book.

*Was there a time when you stopped working on the book but were inspired to get back into through the support of the community?*

That's exactly right! When I left the game industry, that killed a lot of my motivation for the book. Games weren't directly relevant to me anymore, and I didn't feel any pressure to try to pad my r├®sum├® any more.

I stopped working on the book because of that. But people kept emailing for two years asking when new chapters were coming out. That's what got me to start working on it again. If it wasn't for them, it would have died.

*Some of the patterns are quite low level. I thought that was great, but did you ever worry that you might go over the head of the new generation of game developers?*

Not really. I have a lot of faith in the intelligence of most people. If I feel comfortable writing about something, it's because it feels simple to me. It's my job to break the concepts down so that they can have that feeling too.

I don't think there's anything magical or particularly hard about the material, it's just a question of presenting it in the right order and in consumable pieces.

This is one of the main reasons I write at all. I think it's really important for people, especially programmers, to not be intimidated by new domains or ideas. We have a lot of subfields that are scary to people: compilers, OSes, graphics. All of those topics that were brutal classes in your undergrad CS degree and leave people remembering them as overwhelming and horrifying.

But they aren't impossible, and the people who work in those areas aren't any different from you or I. They just put the time into assembling their knowledge one piece at a time. Ultimately, computers just do basic arithmetic. It's all just little functions and pushing bits around. If you can understand that, you can build your way up to the top.

*I loved your code examples. Some really imaginative code in there.*

:D

One of the reasons I wrote this about game programming is because it lends itself to examples that are a lot more fun, concrete, and easy to visualize. The book is, I think, equally useful for business application programming, but it's a hell of a lot more fun reading about wizards and trolls than accounts and employee records.
 
*Were those examples inspired by production code you have worked on? Or were they concocted specifically for the book?*

Yeah, some of them were inspired by games I worked on or hobby games. But the examples themselves were created specifically for the book. This is one of the most difficult and important parts of technical writing. A well-chosen example is more clarifying than a dozen pages of prose, and it's virtually impossible to write your way out of a poor example.

For a lot of chapters, I would spend days mulling in the shower, on drives, during any idle minute trying to come up with the right example before I could make progress.

*What are your thoughts on the current state of the games industry?*

I'm not the best person to ask since I left it about five years ago.

My relationship to games has always been kind of strange. I got into them because I like making games. Most of my childhood was spent with my brother. We'd play with some game or toy for about ten minutes and then spend the next week trying to make our own version of it.

I'm more motivated by creation than consumption, so I was never really a gamer like most in the industry. Also, I came of age in the 2D era, and I love pixel art. When the industry went 3D, it lost a lot of interest for me. I have an awful sense of direction, so whenever I play moderns games, I'm usually just stumbling around lost.

I'm super excited about the rise of small indie games, 2D and retro pixel art. That's all way more my speed.

Part of the reason I was happy to leave the industry is that now I have the freedom to make games as a hobbyist. But then I went and had kids, so now I don't really have time to do that. :)

In general, I feel a lot more comfortable being out of the industry than in. It's a really volatile business and the margins are super narrow. In some ways, it's like being a musician. Most are broke, and a few make it big. It's a rewarding vocation, but it can be stressful making a career out of it.

Especially now that I have a family, I like having a day job that's more stable and treating games as a fun hobby.

## Conclusion

In summary, I really enjoyed this book and can recommend it to any game developer. Patterns are some of the most powerful tools in any developers mental toolkit. *Game Programming Patterns* can help you understand design patterns and generally to become more interested in code design. Then if you still haven't had enough of design patterns, I would highly recommend that you go on to read the classic *Design Patterns* book. 

You can read *Game Programming Patterns* free online, but I would encourage you to buy the book and support Robert's efforts to bring us software development knowledge that is made relevant to the games industry.

Thanks very much to Robert for taking the time to provide thoughtful answers to my questions. I'll be looking forward to updates and new chapters. Perhaps Robert will include my favorite pattern in future editions ;)

## Links and Resources

- Book home page: http://gameprogrammingpatterns.com/
- Free online version: http://gameprogrammingpatterns.com/contents.html
- Amazon: http://www.amazon.com/Game-Programming-Patterns-Robert-Nystrom/dp/0990582906 
- Robert's blog: http://journal.stuffwithstuff.com/
- Git hub: https://github.com/munificent
- Twitter: https://twitter.com/munificentbob


