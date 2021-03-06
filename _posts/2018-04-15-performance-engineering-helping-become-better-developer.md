---
id: 207
title: Performance engineering and how it is helping me become a better developer
date: 2018-04-15T16:44:41+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=207
permalink: /2018/04/15/performance-engineering-helping-become-better-developer/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"a2aee2098a7c";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:112:"https://medium.com/@h4xr/performance-engineering-and-how-it-is-helping-me-become-a-better-developer-a2aee2098a7c";}'
categories:
  - Uncategorized
tags:
  - developement
  - journey
  - optimization
  - Performance Engineering
  - softwae
---
So, it has almost been more than a year since I joined Red Hat as a Performance Engineer. Being a person who loves to code, Performance Engineering may seem like something which involves very less coding and more of testing related stuff. But, this might just not be true. Doesn&#8217;t matter, even if you are a Performance Engineer, you still might be involved in writing quite a lot of code. This code may involve creating automation systems, fixing issues in the projects that are causing performance or scalability problems and many other areas which may not have initially thought of.

As a software developer, one always need to improve upon the architectures while also writing code that is maintainable in the long run and these things take a long time to learn. The more you go deeper into the field, the more you understand about the intricacies of the complex softwares which are being used to manage a large amount of systems, you get to understand why they were built in a certain way and what affected the design decisions.

Now, the question arises, how being a Performance Engineer is helping me become a better developer?

Well, being into the field of Performance Engineering, dealing with issues which are causing huge bottlenecks in performance and hence resulting in poor scalability is a day to day task. These issues can happen due to several reasons, which may include the choice of programming languages, an uncaught memory leak, the way a particular software has been architected, etc. Some of these issues are easy to find and fix, whereas others may require a complete re-write of how a particular component works inside the software. Debugging these issues which are causing performance problems and which happen only at scale, finding their root cause and possibly fixing them takes a lot of time and patience. Working over these issues have proven to be of great help in evolving as a better developer also.

But what did I learn, that is helping me become a better developer? Let&#8217;s see:

  * **Comments and Documentation Matter:** Woah, why is this the first point? The answer to this is, this is the biggest point if you are trying to be successful in the role of a software developer. If you are building something which is not you one night personal project, then commenting your code and providing adequate amount of documentation is the first thing. Just imagine, being given a software and asked to find an issue, you will just keep wandering in the codebase  spanning thousands of lines possibly, to figure out which particular piece of code is causing this issue. In these cases documentation and inline comments prove to be of great help, not just to you as a developer but to anyone else who may need to look into your code in the later period of time.
  * **Your design decisions matter a lot, make them wisely:** While working to figure out performance and scale issues, there have been numerous chances, when we have realized that the underlying code does not have any issue, but rather the way a particular component has been designed is causing the performance and scalability limitations. This tells that a single design choice in your project can alter the way your project behaves forever. Sometimes, these decisions can be so significant, that changing them in the future may require you to re-write a large part of your codebase altogether.
  * **Keep it simple:** Unnecessarily complicating your code doesn&#8217;t reflects of you being a strong developer, rather it shows a negative effect. Writing 5 layers of abstractions for doing a simple thing which could have been done by using a single abstraction layer not only complicates your code and makes it hard to understand, but also negatively affects the performance of your software. All those extra calls, your software will pay the price someday.
  * **Concurrency, implement it only when necessary:** Concurrency is something which can provide a lot of benefits. Imagine, you have built a software which doesn&#8217;t blocks a user to do his other stuff, while the software process the file which has been given to it as an input. It is a great user experience. But, implementing concurrency in software&#8217;s requires a lot of care. One place where you have possibly missed locking a shared variable and you have already dug a grave for the death of your software. The race conditions, the deadlocks, are not something which you will really like to see happening once your software reaches production. These problems are not only hard to debug because there is no one way to reproduce them, but these problems are hard to fix also, sometimes, involving a re-write of some critical components.
  * **Write benchmark tests:** As much as those unit tests are important, so are benchmark tests if your software is a serious candidate for use in really large environments. Those slowdowns of a few milliseconds might not mean anything when it involves 10-20 systems, but may become a big headache, when being deployed on millions of nodes. Benchmark tests help provide an initial idea about which particular method in your code base may be performing slowly.
  * **Code in a resource conservative way:** With technology advancing day by day, the prices of processing power and memory are coming down and down. But this does not mean that the system resources are infinite. Code your software in a way that it doesn&#8217;t exploits the system resources unnecessarily. Use the memory where ever required, don&#8217;t just maintain un-required data throughout the life-cycle of your software run, free it as soon as you know, it may not be required anymore.
  * **Don&#8217;t optimize early:** Many a times, while developing a software we think of optimizing a few pieces early in the stage. This optimization may involve using some hacks, optimizing at the level of intermediate binaries, etc. Most of the time, this early optimization to get a benefit of a few milliseconds reduction in execution time becomes a much larger problem in a long run, where your program is eating through the memory like a crazy snorlax ( Pokemon fan 😉 )

Well, these are a few things which I have learned over the course of last one year and there are things which still needs to be learned and I hope this journey continues.