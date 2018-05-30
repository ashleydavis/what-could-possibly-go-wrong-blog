---
layout: post
title: 'Guest Post: Jump into the Procedural Generation pool. The water is warm.'
image: https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAdHAAAAJGQ1MDQyODg4LTcyMWItNDIzNy1hNGMxLWJmMzhhZDg1MmNiYQ.jpg
date: '2016-10-17 03:20:42'
permalink: jump-into-the-procedural-generation-pool-the-water-is-warm/
disqus_id: ghost-23
---

[First published on Linkedin](https://www.linkedin.com/pulse/jump-procedural-generation-pool-water-warm-alex-norton)

Procedural generation is here to stay. As video games get more and more complex with time, so does the need for developers to provide more content, often to scales beyond the scope of an asset creation team. Sometimes a development team is quite small, as is the case with my own team, and opting for procedural content generation can overcome content output bottlenecks through some cleverly applied math.

Particularly in the last 5 years, procGen has become more and more commonplace, and in more areas than you might realise. In my own project, [Malevolence: The Sword of Ahkranox](http://store.steampowered.com/app/268930), I used it to generate environments, dialogue, items, magic and quests for the players, and - believe it or not - this was a fairly limited use compared to what it's capable of. Legendary game developer John Carmack used procGen to create infinitely scaleable textures with his MegaTexture system for Quake Wars, composer Brian Eno has even developed systems to procedurally generate beautiful ambient music which can be designed, composed and performed by a computer in real-time, making tracks which have never been heard before.

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAf8AAAAJGMxN2U4NGQwLTNkNmYtNGRmOS1iZTVhLWQ4MTFmZDgxZWY2OQ.jpg)

Recently we've had some big procGen titles making waves, such as the controversial No Man's Sky project and even S.P.O.R.E a few years back, and it's fairly clear to see that the reception - while sometimes controversial - is no doubt overwhelming. The key, it seems, is to be ambitious with it.

So when one thinks of procGen, they think of the "magic formula" behind the scenes which runs things, but this is rarely the case. 'Nicely done' procGen often involves a series of layers to form an intricate algorithm of steps needed to produce a "working world" around the player. As such, there are so many different ways to achieve the end result that it can really help exercise a coders' creativity, and is a wonderful area of experimentation for those who are seeking a new challenge to their coding endeavours. I've [written about this topic before](http://www.gamasutra.com/view/news/169624/Opinion_The_difficulties_of_an_infinite_video_game_world.php), but haven't really gone into detail for those wanting to try it out for themselves.

So what's a good starter method to construct a procedural algorithm from scratch? Well, to many who are new to it, the maths can seem difficult - but really, for something simple, it's really not that tricky. I've created this article to show you how a basic one could work.

<iframe width="560" height="315" src="https://www.youtube.com/embed/6KiekKp5mHg" frameborder="0" allowfullscreen></iframe>

Imagine a mechanism like the one above. Every time the handle is cranked, the seed number changes. Because of the complex arrangement of the gears, this produces a fair amount of "chaos" in the number generation, but that chaos can instantly and accurately be reversed by cranking the gear in the opposite direction.

*Author's note: This is a deliberately bad example for the sake of simplicity. Clearly an arrangement of gears such as this would produce a looping number system. I've merely included it as a visual aid to get those new to procGen thinking about the idea.*

Now if we were in a 2D "runner" style game, where we are only operating on horizontal scroll movement, we can generate one screen width worth of level based off of this seed, and link the player's horizontal movement to a turn of the crank. In this way, we can have a game which never runs out of level content, as we aren't keeping track of the player's horizontal co-ordinate in any way. If they move right, crank the handle right. If they move left, crank the handle left and generate the level around the player accordingly.

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAjDAAAAJDE5YTI4M2E2LTQyMGUtNDkwYS1hMjA4LTY5YjY5NTk0MmJlZQ.png)

Oftentimes, people trying to create a particularly large game world (or even infinite, like myself) run into a simple problem with complex ramifications. Data type constraints. When handling your player's co-ordinates in order to feed them into a procGen algorithm to generate a result, you'll eventually "run out of space" in your variables, like what used to happen at the "edge" of the Minecraft world (pictured above). A commonly used variable for co-ordinates is the LONG data type, which can hold a value as low as -9,223,372,036,854,775,807 and as high as 9,223,372,036,854,775,807 - which needless to say is a LOT of wiggle room, and for any practical application would seem to be MORE than enough, since even if you incremented that number by 1 every millisecond, it would still take you 600 million years to traverse the distance. But as soon as you implement some form of quick-travel to your game (which, lets face it, you're probably going to have in a procedural world) you can bridge large swathes of that travel at once and the world suddenly becomes a lot smaller.

Overcoming this issue has puzzled many coders over the years. With our clockwork mechanism above it doesn't matter, as we don't bother keeping track of the cranks. We just perform them. Since no count is kept, we won't run into a data type constraint as our seed value is always a uniform length and calculations of the seed based on a crank turn can be done on the fly. However, obviously if you wanted to use a system like this you'd want to use a much more complex mechanism based off more than just equally spaced gears:

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAhdAAAAJDFhMTUwNzIwLWUzMTgtNDNmNi04ZTFiLTQ4NzJhYTk5MGY5Yg.jpg)

Once this is happening for you, though, you start seeing that layers are required to make a procedural world functional. Things such as anomaly rectification, extra-dimensional factors such as the passing of time, and procedurally fed scripts which fire when certain events happen in the procedural engine. Here is a simple example of layered procedural generation:

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAgMAAAAJDJiMTVkOGFjLTY0MmUtNGQxOS1hYmFkLThjNTljZjgxMWVkMQ.jpg)

The player will only ever see the final result, but applying hand-scripted rules such as "spawn a settlement next to a body of water" or "spawn a road from any settlement to a nearby area of interest" can make an otherwise cold, computer generated area look more hand-made, whereas dynamic factors such as seasons and economy can add even more detail by making the entire game world seem to change and adapt over time, all without you having to hand-design it yourself.

An utterly beautiful - if very complex - version of this can be found in an indie game called 'Dwarf Fortress', as its procedural world generation takes into account not only current dynamic effects such as economy and politics, but also natural evolutionary aspects such as the natural formation of mountains, rivers, forests and even population movements. It is done in such detail that once a new world has been created, you can actually "read the history books" to see what has happened throughout the history of that world which was literally just created a minute ago.

<iframe width="560" height="315" src="https://www.youtube.com/embed/p9a8eqHWbLU" frameborder="0" allowfullscreen></iframe>

I personally like to think of procedural generation as more of an art than a science, as it's quite easy to generate a simple seed and slap that into a heightmap engine, but it's not so easy to take that number and turn it into a scene which genuinely makes the player stop and go "wow". If they can't tell that what they're looking at wasn't hand-designed, then you've done something right.

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAeCAAAAJDQ4MzU1ZDkxLWJmMTUtNDk4My1iYmU2LWQyNDRjMGZhOTJjOQ.jpg)

So, in conclusion, definitely get out there and try your hand at procedural generation. Despite being around for a good 25-30 years in video games, it's only really started to blossom recently, and as more people get involved, the techniques and methods used will grow and blossom with it.

So get coding, and I'll see you on the indie sites!

# About the Author

Alex Norton is an indie developer from Brisbane, Australia where he works full-time as both the sole driving force behind indie game studio Visual Outbreak as well as the design half of mobile app duet Fluffy Knuckleduster. He made a name for himself for his work on the infinitely procedural rpg, Malevolence: The Sword of Ahkranox, and has since become infamous for remaining staunchly 'purist' indie and giving long-winded lectures on game design and technology whenever he is given the chance - and often when he's not given one.

