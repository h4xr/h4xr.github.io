---
id: 162
title: Handling Crashes in Microreg
date: 2017-02-16T19:25:03+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=162
permalink: /2017/02/16/handling-crashes-microreg/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"6e233d0db295";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:66:"https://medium.com/@h4xr/handling-crashes-in-microreg-6e233d0db295";}'
categories:
  - Projects
tags:
  - Crash Management
  - Microservices
  - project
---
From quiet some time, I have been working on building a toolset for developers that can help ease their pain of deploying and managing their Microservices based infrastructure. You can take a look at the following posts to know what exactly is going under the hood 😉

  1. [Building a Microservices Registry Server (Part 1)](http://saurabhbadhwar.xyz/blog/2016/11/19/building-microservices-registry-server-part-1-architecture/)
  2. [Building a Microservices Registry Server (Part 2)](http://saurabhbadhwar.xyz/blog/2016/12/01/building-microservices-registry-server-part-2-api-gateway/) 

An important part to any successful tool is how gracefully it can handle errors. And in case of our Registry Server, this thing becomes very crucial. Even after having extensive error checking we cannot rely on the mere fact that our Registry Server will never crash and will always behave as a good being. So, before diving into what can happen wrong and how we are trying to handle it, let&#8217;s look at what can cause our Registry Server to crash:

  * Even after extensive checking of service registration data, there could still be some internal corruption that may cause the service registry to crash
  * 99% of the time, the underlying system on which our service runs can be reliable, but we have to account for that 1% too
  * The registry server consists of different subcomponents that run as separate subprocess and communicate with each other. The failure in the subcomponents may require a restart of the Registry Server.

So, we saw what are the possible reasons that can bring the Registry Server down, now, what can be the implications of this? Well, there are quite some and they can be serious:

  * Since, Registry Server is the only location which keeps the track of which services are running, where they are running, a crash in it can lead to lots of services losing their caretaker 🙁
  * Different components of the toolset interact with the Registry Server to find where to route the incoming requests, and in case the Registry Server goes down, these components will be left stranded in the middle not able to serve any request, even when the services are still up
  * The ServiceDB is a completely in-memory database, a failure in Registry Server will cause the service records to be lost forever.

The above implications can be quite serious and can cause a major headache and we need to tackle this. For handling the stuff, there was a requirement for having a feature that can gracefully handle these implications. In case there is a crash, the Registry should be restored to the previous stable state when it is taken back online.

#### Crash Manager

Yes, this is our solution. CrashManager is a crucial component that has been baked in recently into the codebase of Microreg. The role of the crash manager is to constantly backup the data of the ServiceDB into a secondary storage where it can be kept safe in case a crash occurs. Whenever the Registry Server comes online after a crash, the backed up data is read by the CrashManager and restored to the ServiceDB effectively restoring the state of the Registry Server.

One important design consideration for the crash manager was that, it should not block our Registry Server while it backs the data up to the secondary storage. This design consideration was taken care by running the Crash Manager as a separate daemon thread in the background, which constantly backs up the data at regular time intervals. The advantage we have while running the CrashManager as a daemon thread is that, it will exit automatically whenever the main thread is terminated.

#### Future Goals

CrashManager is an important part of the project and will see a few major additions in the very new feature. Somethings that are currently planned are as follows:

  * Implement a way through which Registry Server replication can take place
  * Move from time based data backup to event based data backups (such as registration of a new service or removal of existing one)
  * CrashManager should integrated more deeply with the other subprocesses that are being run as a part of Registry Server so as to allow for more modular Service recovery mechanism

The current codebase of the project can be tracked at [Github](https://github.com/Microreg)