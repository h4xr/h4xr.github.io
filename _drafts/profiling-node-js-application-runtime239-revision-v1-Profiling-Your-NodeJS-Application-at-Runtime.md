---
id: 240
title: Profiling Your NodeJS Application at Runtime
date: 2019-03-24T13:48:34+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2019/03/24/239-revision-v1/
permalink: /2019/03/24/239-revision-v1/
---
As developers, we mostly focus on the area of developing applications in the best possible manner. This includes the choice of languages, tools, algorithms, etc. whichever helps us get the best possible outcomes while also keeping the productivity in check. These applications can be the desktop GUI applications, system utilities or Web applications which provides the user the power to achieve their tasks with ease. This development process also involves optimizing the codebase once the slow areas inside an application are identified. But how many time do we take a look at the data about which specific functions or methods are performing slow and are being used frequently? The data related to identifying the hot methods in the codebase could be of great help when it comes to dedicating an effort to optimize our applications.

This post covers the process of how we can profile our NodeJS applications during runtime without really interrupting the applications. Also, since the application is not interrupted at all, we can run this in production to identify the performance hotspots while the application serves the live traffic. But, why am I targeting NodeJS here and not some other language? The answer to this lies in the fact that, recently I have been involved in trying to find out a way through which we can profile a running NodeJS server side application without introducing a restart to the application or generating too much overhead that may cause the application to slowdown causing disruption in the production environment. As this post builds up, we will get to dive deeper into the concepts of how this profiling is made possible. So, let&#8217;s get going.