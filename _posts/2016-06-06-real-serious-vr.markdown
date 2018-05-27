---
layout: post
title: Real Serious VR - Live action video with CG background, captured to 360 video
date: '2016-06-06 06:24:00'
permalink: real-serious-vr
---

<img src="/content/images/2016/06/Real_serious_VR.png" style="width: 750px;"/>

[Real Serious Games](http://www.realseriousgames.com/) (RSG) recently launched (in January 2016) its most recent commercial [virtual reality](https://en.wikipedia.org/wiki/Virtual_reality) project.

This VR experience is an inspirational *tour* of career options. It shows young people the options available to them in the workforce. It is described as an aspirational and immersive [360 video](https://en.wikipedia.org/wiki/Immersive_video) virtual reality experience. Live action interviews with virtual *guides* are composited over a virtual animated background. The participant feels as though they are in the scene with their virtual *guide* and they can look around freely to see what else is happening in the scene.

<img src="/content/images/2016/06/20160128_124618.jpg" style="width: 750px;"/>

This article accompanies [the talk we gave](https://www.youtube.com/watch?v=64pyHKfeuY0) for the [Brisbane VR Club](https://www.facebook.com/groups/virtualrealitybrisbane/) on March 22nd. Thanks to the Brisbane VR Club for letting us share our story with the VR community in Brisbane. With the video and this article we can now share our knowledge with the larger VR community. 

We love working with VR. What's most amazing about VR is that we get to help write the rule book as we go. We are building new and innovative experiences and we are pioneers forging ahead in this new and exciting industry.

# Contents

- [The VR experience](#thevrexperience)
- [Development](#development)
- [Concerns, considerations and design choices](#concernsconsiderationsanddesignchoices)
- [Technology: Platform and VR hardware decisions](#technologyplatformandvrhardwaredecisions)
    - [Gear VR: The good parts](#gearvrthegoodparts)
    - [Gear VR: The not so good parts](#gearvrthenotsogoodparts)
    - [Why not the Oculus Rift?](#whynottheoculusrift)
- [Filming, 360 video capture and post-production](#filming360videocaptureandpostproduction)
- [360 Video Playback](#360videoplayback)
- [Telemetry](#telemetry)
- [RSG](#rsg)
  - [The company](#thecompany)
  - [Our virtual reality history](#ourvirtualrealityhistory)
  - [Virtual reality development](#virtualrealitydevelopment)
- [Conclusion](#conclusion)
- [Links](#links)
- [About the Reviewers](#aboutthereviewers)

# The VR experience

This project was a federal government funded project that was launched in January 2016 after several months of rapid development.

<img src="/content/images/2016/06/image000002.jpg" style="width: 750px;"/>

The VR experience is touring remote, regional and urban communities in Queensland in a dedicated bus and will continue to do so for many years and may also be deployed in other states. The experience is designed to help young people understand the careers and industries that are available to them. *Trainers* run sessions where 6-8 *participants* are taken through the VR experience in a group.

<img src="/content/images/2016/06/Screenshot_20160224-131410.png" style="width: 750px;"/>

Real Serious Games designed and delivered this VR experience. We designed and built the art pipeline and the interactive 360 video player mobile application. We filmed the interviews with the virtual *guides*. We modeled and animated the background scenes. We composited the live action and the CGI and captured virtual 360 videos. We designed the layout of the touring bus and we supervised construction of it. Finally we installed the hardware and software on the bus, tested that it works and have since provided support and maintenance.      

<img src="/content/images/2016/06/image000001.jpg" style="width: 750px;"/>

The VR experience was developed to run on Samsung Gear VR. For maximum flexibility we built a custom 360 video player using Unity. The project was an art-heavy job, with a large emphasis on the film and art production pipelines. The art team filmed, built scenes and content and captured the virtual 360 videos. 

The development team started with VR research and development and later transitioned to art pipeline support. We built innovative debug, testing and VR experience tracking (telemetry) tools. Ultimately the project moved to a support phase, with bugs being fixed and performance tweaked by the development team.

The development team's main priority was to get 360 videos playing on the target device. Our aim was to support the art team and provide a system with fast turnaround for updating the videos that have changed. We'll delve into the technology and process in the *Development* section below.

# Development

## Concerns, considerations and design choices

Real Serious Games is a client-focused business: delivering a high quality result within a set time frame is very important to us. We knew that we would have limited time to complete the project. How would we deliver something exceptional in the time that we had?

Virtual reality development is a minefield of technological risk. Being a whole new realm of development we are making up the rules as we go. We are working with new technology that requires very different design decisions to traditional games and simulations. As developers we must address many issues. Working with cutting edge technology means that we had to carefully balance the risk so that technological issues didn't derail the project.   

The largest problem with VR is its potential for making people sick. This topic is [already well documented](https://en.wikipedia.org/wiki/Virtual_reality_sickness). From our perspective we absolutely need to minimize the number of people who feel motion sickness while in VR. By extension we need to maximize the number of people who are able to use virtual reality. We have been very conservative and thoughtful in our design choices relating to this issue. We understand that when a person feels ill from VR that they will be very reluctant to ever go back in VR again.

Good performance is a must and is at the heart of all our VR design decisions. Bad performance in interactive 3D applications should generally be avoided in any case, however usually there is some flexibility. In VR there is much less flexibility where performance is concerned. You *must* have adequate performance or risk making your user suffer virtual-reality sickness.

Portability was also a big concern for us. Gear VR is portable and has minimal footprint. It is easy to store these devices while the bus is in motion. The Oculus Rift in comparison takes a lot more space. It comes with a mountain of cables and must have an entire PC to run the experience. We had limited space in which to run 6-8 experiences side-by-side. If we had used Oculus Rift it would have been very cramped inside the bus. Also having 6-8 PCs running would have increased the heat levels in the bus. This isn't good considering the bus will be touring in a hot environment (North Queensland). The bus is air conditioned, but temperature and overheating of devices was still a big concern, even considering our choice of Gear VR. 

Our choice of Gear VR for portability increased the risk for bad performance. Mobile devices (we used the [Samsung S6](https://en.wikipedia.org/wiki/Samsung_Galaxy_S6) to power the Gear VR) have far less processing and graphics power than a desktop PC that would run an Oculus Rift.   

<img src="/content/images/2016/06/Samsung_Gear_VR.jpg" style="width: 250px;"/>

We took steps to address our performance risk. We decided to discount performance issues entirely by crafting the experience from 360 videos. We still had to ensure the 360 video played at a reasonable framerate, although we expected this to be significantly easier to achieve than trying to optimize an interactive 3D build for mobile. [Optimization](https://en.wikipedia.org/wiki/Program_optimization) is a difficult and time-consuming process and we were concerned that it would hinder our ability to deliver this project on time. Instead of focusing on optimization we were instead able to focus our time on delivering a quality experience rather than making it run at a decent framerate.

The choice to use 360 video also means that our creative people had more artistic control through their tools for editing videos and [post-production](https://en.wikipedia.org/wiki/Post-production) work. Using 360 video meant that there was a fixed perspective on the 3D world, therefore there was no need to *move* the participant through the 3D world, something that can so easily cause motion sickness in VR. So that was a positive design decision that was made for us based on our decision to use 360 video. We must still transition the participant from video to video, but that is easily done by fading to black before *teleporting* them to the next *scene*.

The experience hinged on video playback and our ability to capture 360 video from a virtual scene with embedded live-action video. For various reasons we already knew we would not use Unity's built in video playback. For a start when you import a video into Unity it is recoded to Unity's format. We weren't happy with the quality of this format and wanted our own choice of codec but Unity does not allow this. In addition we didn't want to *bake* the videos into the build. This would make the [Android build](https://en.wikipedia.org/wiki/Android_application_package) (APK) large and unwieldy. This would slow down the development process. It means that if one video needed to change, the entire APK would have to be *built* and *copied* to each Gear VR device. We knew that we would need to update videos many times before we were happy with the final product. A tight feedback loop is essential for an effective and efficient development process. We aimed to minimize the time between changing a video and then being able to test that change on device. 

Our approach not to bake the videos into the APK also had a positive effect on the feedback loop with our client. We were able to deliver a single APK to the client, then we delivered videos as they became ready for feedback and approval. The client was able to suggest changes and we were easily able to update particular videos and copy just the videos back to the client without having to send them a new build of the APK. If we had baked the videos into the APK then we would have had to send the massive APK to the client each time a video was changed, no matter how small the change.

We used 3rd party solutions for streaming video playback. We knew how complex and time-consuming it would be to create this ourselves, so we bought solutions for PC and Android from the [Unity Asset Store](https://www.assetstore.unity3d.com/en/). We bought separate solutions for PC and Android because we couldn't find a package that covered both platforms. We needed Android because that was the target platform for the product. However, we also needed a solution for PC to use when capturing 360 video and for convenient testing of the product on our development workstations. 

Our decision to deliver the product on mobile had some ramifications. Mobile devices run hot when displaying 3D content. We needed to understand the temperature of the device, how to keep it cool and how when to let it rest to cool down. We also needed to understand the battery life of the devices when running 3D content. How long could we run a device before we exhausted its battery? We mostly covered this by using data to understanding the limitations of the devices and developed a plan to stay within these limitations. The telemetry system, covered later, was instrumental in developing this understanding. 

## Technology: Platform and VR hardware decisions

This VR experience was developed for [Samsung Gear VR](https://en.wikipedia.org/wiki/Samsung_Gear_VR) and powered by the [Samsung Galaxy S6](https://en.wikipedia.org/wiki/Samsung_Galaxy_S6).

Multiple Gear VR devices (6-8 normally) are connected to a wireless [local area network](https://en.wikipedia.org/wiki/Local_area_network) (LAN). The router can be connected to the 3G cellular network when in range. This allows the network to be connected to the internet which is useful for remote support purposes.

Also connected to the LAN is a PC running Windows 7. This is a server that collects remote telemetry from each of the devices and saves the data into a MongoDB database. This PC is connected to a large screen in the touring bus and displays the realtime data feed from the devices. It can also be used to generate reports from data stored in the database.

<img src="/content/images/2016/06/Structure.png" style="width: 550px;"/>
     
Throughout the project we have maintained a PC build of the VR experience. Of course for any project like this you *must* test on the target platform. However it's worthwhile maintaining a PC build for more convenient debugging and testing. When you are certain that the code works on the PC build you must also test it thoroughly on device. Testing on device is slow. Development is an error prone process. The cycle of code, test, notice bug, fix bug, test again can be very slow, laborious and wasteful when you are only testing on device. Using a PC shortens the feedback loop and massively speeds up the development process. Having a PC build also helps when you happen to have more developers than devices! It means you can have everyone being productive even when there isn't enough devices to go around.

### Gear VR: The good parts

So why gear VR? Gear VR being *mobile* is portable. It has no cables. Anyone who has used an Oculus Rift will know that you can easily get caught up in the cables. Gear VR gives you more freedom of mobility and orientation. Can you imagine 6-8 participants in confined space? Everyone is going to end up tangled in everyone else's cables.

Running 3D on mobile can result in excess heat. That's something that that needed careful management to avoid overheating. This is especially true in the hot climate of North Queensland even when the bus is air conditioned. Even so, can you imagine how much hotter it would be with 6-8 Rifts and their PCs packed into the bus? Not to mention the amount of extra space they would take up and the need to pack them away when the bus is in motion.

The combination of Gear VR and S6 is a very robust and reliable product. Samsung have done a great job with this product.    

### Gear VR: The not so good parts

The Gear VR, being a *mobile* device, has much less processing power and graphics capability compared to a desktop computer that would power an Oculus Rift. To start you can't expect to make something as impressive on the the Gear VR as it would be on a Rift. Making an interactive 3D application or a game for mobile requires careful attention to detail and optimization capability. This can add much time (investigation, coding & QA) to your process and shouldn't be taken lightly. Good performance in games is key to having a good user experience. This is especially true for VR development.

As already mentioned we completely worked around the performance problem by choosing 360 videos. 

### Why not the Oculus Rift?

We have built successful products on the Rift previously, so we have nothing against the Rift per say. The Rift has been difficult to work with in the past with problematic SDK and Unity integration, unstable drivers and a less-than-friendly developer interface. However recently this has improved so much that you can now plug in your Rift and (provided the drivers are installed) just enable the *[Virtual Reality Supported](https://unity3d.com/learn/tutorials/topics/virtual-reality/vr-overview)* checkbox in [Unity Player Settings](http://docs.unity3d.com/Manual/class-PlayerSettings.html). That's really all there is to it. That's how simple it is to get started with VR these days.

With our previous experience developing for the Rift we very tempted to continue with that device. But the need for a portable device and small form factor made go with the Gear VR.  
      
## Filming, 360 video capture and post-production

The live filming was not 360, the guides in the experience were filmed using a normal digital video camera: a Sony Alpha A7S mark II mirrorless digital camera.

<img src="/content/images/2016/06/IMG_4133.JPG" style="width: 750px;"/>

The guides were filmed with a green screen in the background to be able to composite the live video over the CG background. We recommend using a good quality green screen. However it might be possible to get by with something cheap. You can pick up a green screen for around $200 AUD on Ebay and that might be good enough to get you started.

<img src="/content/images/2016/07/IMG_4316.JPG" style="width: 750px;"/>

The process of capturing the 360 videos was actually done in Unity. The video of the virtual guide is rendered to a texture and played back on a quad (a simple [polygon mesh](https://en.wikipedia.org/wiki/Polygon_mesh), often called a *billboard*) that is placed in the [Unity scene](http://docs.unity3d.com/Manual/CreatingScenes.html). The billboard is composited on the CG background using alpha maps and a custom [shader](https://en.wikipedia.org/wiki/Shader). The combined live video and animated CG background is captured, directly from the Unity scene, to a 360 video.  

For the project we actually had two Unity projects. One project was for building the 360 video player, the other was dedicated for capturing 360 video. We used Unity's [reflection probe technology](http://docs.unity3d.com/Manual/class-ReflectionProbe.html) that is usually used to capture the scene for [reflections and environment maps](https://en.wikipedia.org/wiki/Reflection_mapping). I thought it was amazing that this technology can be co-opted to produce 360 videos.

We captured the 360 video in mono. We found that stereo caused eye strain. This might be because it's not possible to adjust the [interpupillary distance](https://en.wikipedia.org/wiki/Interpupillary_distance) (IPD) when using pre-recorded 360 video. This is the kind of thing that makes people sick. So we stuck with mono to be safe. As a bonus the capture process is much quicker with mono.

Post production of the 360 videos was done in house at Real Serious Games using Adobe [Premiere Pro](https://en.wikipedia.org/wiki/Adobe_Premiere_Pro) and [After Effects](https://en.wikipedia.org/wiki/Adobe_After_Effects).

## 360 Video Playback

We created a custom 360 video player using Unity. This was built from a separate Unity project than the one used for capturing. Building a Unity app allowed us much more control, flexibility and opportunities for customization. This means we could add whatever interactivity that we deemed necessary. It didn't just have to be a sequence of 360 videos. The sequence of videos can be determined from the actions of the participant. We wouldn't have this kind of flexibility and control if we had used an off-the-shelf 360 video player.

As already mentioned we used plugins from the Asset Store for the video streaming. For the Android build we used [Easy Movie Texture](https://www.assetstore.unity3d.com/en/#!/content/10032) and for the Unity Editor and PC build we used [AVPro Video](https://www.assetstore.unity3d.com/en/#!/content/57969). We used 3rd party plugins because Unity's default handling of video assets wasn't appropriate for this project. We wanted to use a codec that was not supported by Unity. In addition we weren't happy with Unity's default processing of imported videos. The Unity import process makes changes to the quality of the videos that we weren't happy with, it also takes far too long. As if that wasn't enough committing large videos to version control quickly makes a repository large and unwieldy, we knew this would cause problems. Also consider how big the build would be when packed with large videos. Making a single change and sending an updated video to the client is time-consuming and chews through internet bandwidth. Imagine copying a build containing 20 videos, forget about that! 

We needed to have fast turn-around on changes to videos. This meant that we simply could not bake videos into our build. Having to rebuild to include small changes to videos would have been an unnecessary impediment in our process. The build would take a long time to create and it would take a long time to copy to device for testing. Any change, no matter how small, would result in a new massive build that might have to be copied to our client for testing. 

So having the videos separate to the build was necessary. To support this we created a [custom Android plugin](https://github.com/Real-Serious-Games/Unity-Android-Plugin-Example) to allow the app access to the SD card file-system. This allowed us to stream videos from the file-system. Testing a new video on device simply meant copying the updated video to a special directory on the SD card.    

For rendering the 360 videos we render the video to a texture, the texture is then mapped onto a sphere that surrounds the camera. Early in development we realized that Unity's default sphere primitive wasn't up to the job. We also had problems with 3D Studio Max's Geosphere. Procedurally generated spheres often have a big problem with geometry at the top and bottom of the sphere that *Adam Single* calls the *magic mirror* problem. The UVs at the top and bottom are pinched which causes distortion and warping for anything that passes through these areas in the video, think of the effect you get in a [*mirror maze*](https://en.wikipedia.org/wiki/House_of_mirrors).

<img src="/content/images/2016/06/Magic_mirrors.png" style="width: 550px;"/>

Fortunately an answer to the sphere generation problem wasn't far away. We found it at the totally awesome *[Catlike Coding](http://catlikecoding.com/)*. This website has many useful coding tutorials with lots of details, images and screenshots. There is a [sphere generation tutorial here](http://catlikecoding.com/unity/tutorials/octahedron-sphere/) that shows how to create sphere geometry. It starts with an octahedron and [tessellates](https://en.wikipedia.org/wiki/Tessellation_(computer_graphics)) down to the required resolution. This kind of tech is designed to texture map procedurally generated planets. We found that it works perfectly for generating spheres for 360 movie playback. For this project we had the luxury of being able to create a very high resolution sphere. We could get away with that because the sphere was the only geometry in the scene, so performance wasn't an issue. 

<img src="/content/images/2016/06/Procedural_planet.jpg" style="width: 350px;"/>
      
As for audio, we decided to keep it simple and we used stereo audio. If we had built a fully interactive real-time 3D application we would have used 3D sounds positioned within the scene. This is the technique that provides immersive audio for games. Given our choice for 360 movies though, we were unable to use the 3D sound technique that is normally used in game development. 

Because the sound had to be recorded, individual sounds could not easily be positioned within the scene. For our purposes stereo was good enough, however the stereo illusion is fairly easily broken, although most people in the VR experience didn't seem to notice that they are not experiencing full 3D audio. We would like to have used [binaural recorded audio](https://en.wikipedia.org/wiki/Binaural_recording) but this technology for VR isn't mature enough yet. We avoided 360 audio because it represented too much technical risk, although we have no doubt that fully 360 recorded audio will soon be a solved problem.   

## Telemetry

We built a telemetry system to help understand and monitor what is happening in the VR experience. Data is streamed from multiple Gear VR devices to a server, the server aggregates the data and stores it in a database. The VR devices continuously report their status to the server over the local area network (LAN). Using this system we can monitor real-time metrics such as device temperature, battery level, frame rate and more.

<img src="/content/images/2016/06/Telemetry_structure.png" style="width: 550px;"/>

The telemetry dashboard allows the trainers to better understand the progress of participants through the experience. They can understand what they are looking at and if they are indeed looking where they are supposed to. This is invaluable knowledge for our client, especially considering it would be very difficult otherwise, specifically with the Gear VR, to know what a participant is currently doing in the experience.

<img src="/content/images/2016/06/Telemetry_realtime.png" style="width: 750px;"/>

<img src="/content/images/2016/06/Look_at.png" style="width: 550px;"/>

Telemetry data is output from Unity by a [simple C# library](https://github.com/Real-Serious-Games/Metrics). The data goes over the wire in [JSON](https://en.wikipedia.org/wiki/JSON) format via the [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) protocol. Telemetry data is received by a [simple Node.js server](https://github.com/Real-Serious-Games/Metrics-Server) running on a PC on the LAN. Telemetry data is then displayed in realtime in the *telemetry dashboard*.

All incoming telemetry data is recorded to a [MongoDB](https://en.wikipedia.org/wiki/MongoDB) database that is also running on the PC. From the *reporting page* of the telemetry dashboard it is possible to look at session and daily summaries of activity. At the click of a button, reports can also be downloaded in CSV format and from there can be loaded into Excel for further analysis.   

<img src="/content/images/2016/06/Telemetry_reports-1.png" style="width: 750px;"/>

We always recommend collecting as much raw data as possible. Doing so allows for future data analysis. You don't know it yet, but you may want to make use of that data in the future. Just because we don't know yet how we might later want to use the data, doesn't mean we shouldn't start collecting it now. It's too late in the future to decide that we want to do some data analysis only to discover that we didn't have the foresight to record the necessary data. With a full set of data recorded over time we can do reconstructions and visualizations at a later date. This could be very useful for the project in the future. As an example consider that we record the direction the participant is facing. At some later time we can use this data to produce an animated heatmap, overlaid on the video, that shows the path of the participant's attention as they progressed through the experience.  

# RSG

## The company

[Real Serious Games](http://www.realseriousgames.com/) create a variety of digital interactive products and real-time visualizations in the following areas: 

- Virtual reality
- Serious games
- Advanced E-learning
- Interactive planning
- Forensic animation

<iframe width="560" height="315" src="https://www.youtube.com/embed/oUAcaAFs66s" frameborder="0" allowfullscreen></iframe>

The company was founded by Karen Sanders and Andrew Goldston and has been operating for over 7 years. In that time we have completed more than 200 projects. We move fast and complete projects quickly. We have high standards and we are completely customer focused. We work closely with clients to deliver high-quality work as quickly as possible.   

## Our virtual reality history

At Real Serious Games we don't just do virtual reality: VR is one of our multiple product lines. Alongside VR and immersive videos we produce simulations, visualizations and serious games. This article is about virtual reality so in this section I'd like to highlight our previous VR projects.

We started in virtual reality with a demo for the [Simtect](http://www.simulationaustralasia.com/events/simtect) conference held in Brisbane in 2013. We pulled together a demo from previous RSG simulation and visualization projects. This was intended to answer the question *what would a Real Serious Games virtual reality experience look like?* It was a convenient starting point because we had all the assets and code and just need to get it into VR. Easy right? Not so much. The [Oculus Rift](https://en.wikipedia.org/wiki/Oculus_Rift) DK1 was relatively new. The [drivers](https://en.wikipedia.org/wiki/Device_driver) were unreliable and difficult to work with. The [SDK](https://en.wikipedia.org/wiki/Software_development_kit) was far from mature and there was a mess of problems in the [Unity](https://en.wikipedia.org/wiki/Unity_(game_engine)) integration. So we had our work cut out for us. 

<img src="/content/images/2016/06/Oculus_DK1.png" style="width: 350px;"/>

We pushed through the problems and rapidly built the demo. We were one of only a few companies demonstrating VR at the Simtect conference. Most conference delegates had not yet tried virtual reality. We demonstrated the experience in the RSG stall it generated substantial interest. In those couple of days we introduced many people to virtual reality. 

<img src="/content/images/2016/06/Simtect_demo.png" style="width: 750px;"/>

Despite the problems with the DK1 and it's less than desirable visual quality we managed to generate enough interest in VR that in 2014 we were able to ship a commercial VR product based on the DK1. Virtual reality was such a promising technology that our client was able to overlook the inadequacies in the hardware. We built a VR training simulation of a [tunnel boring machine](https://en.wikipedia.org/wiki/Tunnel_boring_machine) (TBM) for [North West Rail Link](https://en.wikipedia.org/wiki/Sydney_Metro_Northwest). 

These machines are big, expensive, dangerous and operate deep underground. It's an almost perfect use case for virtual reality training. The simulation was a *multi-player* experience. A *trainer*, kind of like a tour guide, takes the *training group* on a virtual tour of the TBM. Using a combination of visual and audio cues the trainer is able to direct the attention of participants to areas of interest. The trainer and the training group are in the virtual world together. They have a closed audio link for communication. This means that the group doesn't have to be in the same room together, they could in fact be anywhere in the world separated by great distance. Follow this line of thought and you will see that VR has great ramifications for the training industry.      

<img src="/content/images/2016/06/TBM_VR.png" style="width: 750px;"/>

Image courtesy of ABC's Catalyst program.

In our next VR project we built a new experience based on the tunnel boring machine. This new project was not for training but to demonstrate the TBM to the community and educate them about the tunnel construction project. This had very different requirements to the original TBM training simulation. By this time the DK2 has been released and so this project was built to work on it. The DK2 had much better visual quality, but unfortunately it seemed to add more technical problems into our development process. We worked around the issues as we must do when working with cutting-edge technology and delivered the *NWRL community* edition. 

<img src="/content/images/2016/06/Oculus_DK2.jpg" style="width: 350px;"/>

The use of VR in this situation gained much acclaim. Check out the following video and you'll see it an action when it appeared on [ABC's Catalyst program](https://en.wikipedia.org/wiki/Catalyst_(TV_program)):

<iframe width="560" height="315" src="https://www.youtube.com/embed/pz8cub1UPUE" frameborder="0" allowfullscreen></iframe>

Our next commercial project was again built on the DK2. Also in 2014 we produced a virtual reality experience that showed the operation of a sodium cyanide manufacturing plant. Our client [Synergen Met](http://www.synergenmet.com/about/) builds these massive machines. They can operate this onsite to produce cyanide. It probably goes without saying that it is much safer to produce this dangerous material onsite than transporting it through populated areas. This equipment is large, complicated and it can be very difficult to explain. However in virtual reality you can *show* how the manufacturing plant works in 3D with animations and annotations to aid understanding. The use of virtual reality has made it much easier for Synergen Met to explain their product to their customers.  

<img src="/content/images/2016/06/Cynaide_skid.png" style="width: 750px;"/>
  
Towards the end of 2015 we have built our most recent VR product which was developed for [Samsung Gear VR](https://en.wikipedia.org/wiki/Samsung_Gear_VR) and has been the focus of most of this article.

## Virtual reality development

Since the introduction of the Oculus Rift DK1 Real Serious Games has delivered multiple commercial virtual reality products. During this time we have followed the blossoming VR industry and been proactive in our research and development. As the market for virtual reality has grown and matured we formed a *VR research committee* within the company. Members of the committee took responsibility for different areas of investigation. Research activities were conducted between meetings of the committee: Information gathering, prototyping and knowledge building. Findings were then discussed in the meetings and direction set for future research and development. 

<img src="/content/images/2016/06/IMG_20160314_143635.jpg" style="width: 750px;"/>

Research and development is active at Real Serious Games between VR projects. When the time came to build a new VR experience we were well equipped with knowledge and understanding to select technology and make meaningful decisions.

Every member of Real Serious Games team (and sometimes friends and family) have at some point or another been a tester for our VR products. This gives us the ability to get new people into a particular VR experience for a fresh perspective and also to ensure that a range of people can undertake the experience without feeling ill. It's our long standing tradition that we crowd around our VR beta-testers and take silly photos.

<img src="/content/images/2016/06/20160107_114917.jpg" style="width: 750px;"/>

At Real Serious Games we use [Slack](https://slack.com/) for chat and discussion among team members. We have a channel dedicated to virtual reality. The channel is very active because there is always news and events in the industry. New headsets, input devices, SDKs and demos are constantly being released. Something is always happening. There is a seemingly constant stream of VR company announcements and other industry news. Our Slack VR channel is a way for everyone to share knowledge, contribute intel and develop our shared understanding of the VR industry.

At Real Serious Games our products are usually built using Unity with development in [C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)) and [Visual Studio](https://en.wikipedia.org/wiki/Microsoft_Visual_Studio). We often build products that span multiple platforms and have been known to work with a broad spectrum of technologies. 

We have built web apps in [JavaScript](https://en.wikipedia.org/wiki/JavaScript), [HTML](https://en.wikipedia.org/wiki/HTML) and [CSS](https://en.wikipedia.org/wiki/Cascading_Style_Sheets). We have built cloud servers using JavaScript, [Node.js](https://en.wikipedia.org/wiki/Node.js) and [MongoDB](https://en.wikipedia.org/wiki/MongoDB). We have built native [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) plugins and apps using [Java](https://en.wikipedia.org/wiki/Java) and [Android Studio](https://en.wikipedia.org/wiki/Android_Studio). 

The RSG art team uses [3DS Max](https://en.wikipedia.org/wiki/Autodesk_3ds_Max) and [Premiere Pro](https://en.wikipedia.org/wiki/Adobe_Premiere_Pro).

# Conclusion

In this article I have talked about the Real Serious Games' most recent virtual reality project. I've covered our virtual reality history and the commercial projects we have launched to date. I've talked about our VR process and discussed some of the most important factors of the development of our most recent project.

Thanks for taking the time to read the article. If we can help you in anyway relating to serious games, simulations and virtual reality experiences, please get in contact via ashley.davis@realseriousgames.com.

# Links

- Real Serious Games: http://www.realseriousgames.com/
- Talk for Brisbane VR Club: https://www.youtube.com/watch?v=64pyHKfeuY0
- Open source code: https://github.com/real-serious-games/

# About the Reviewers

## Andrew Goldston

Andrew is Founder and Operations Director of Real Serious Games.

## Adam Single

Adam is a passionate game developer and a senior member of the Well Placed Cactus development team. He's an organiser for Game Technology Brisbane, Game Development Brisbane and Brisbane Unity Developer meetups. 

For more info please see: [https://branded.me/adamsingle](https://branded.me/adamsingle)

