---
id: 115
title: The Rise of Microservice Architecture
date: 2016-09-04T12:22:51+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=115
permalink: /2016/09/04/rise-microservice-architecture/
categories:
  - Uncategorized
tags:
  - Architecture
  - Distributed applications
  - Microservices
---
Microservices is a new term that has recently sprung to life. The term is used to describe a new architecture in designing and building new software applications as small and independent set of services which can be independently deployed and managed.

In the Microservice architecture(MSA), the size of a service is kept into check. More specifically, a microservice architecture aims at services which are small in size and where the accompanying protocols are lightweight. This allows the developers to build services that are working to achieve a particular set of tasks rather than coupling all the tasks into a single monolithic process.

#### The Advantages of Microservice Architecture

  * The Microservice Architecture helps in increased cohesion between different services and reduced coupling. Together this gives rise to improved maintainability of the application and easy modifications.
  * More focused approach: By using MSA, the teams can focus on building individual services that accomplish a particular set of tasks.
  * Increased set of choices: With MSA, the implementers are free to choose the programming language and tools that seem fit for implementing a particular service.
  * Continuous Delivery Model: In contrast to a single delivery model, where the whole application is delivered as one package, using MSA, the applications can be delivered in a continuous manner as more services are built and deployed.

Certainly, MSA has lot of advantages be it from the application design to providing granular services that can be independently deployed. But, nothing is perfect, and while working on something, we need to know both it advantages and the issues we might face.

#### Overhead in Microservice Architecture

While deploying a Monolithic application, you can choose a small server cluster where the application will run and scale. But, in MSA, you have got a number of services that now need deployment and maintenance. Added to this is the fact that these services could have been written in a variety of programming languages, this can sometime prove to be a pain. Whereas in a monolithic application, you have to maintain a single application, now you need to ensure that all the services are running and responding to the requests, since failure of one service to respond may lead to the whole application misbehaving.

One other issue and an important one that pertains to the MSA is the problem of Network Latency. Since, different services in MSA are reliant on the network to communicate with each other, network latency can prove to be a problem. If the service messages are delayed between two interacting services, it just may lead to synchronization problems between the different parts of the application.

These a just a few overheads while working in MSA which have been addressed through various means and can certainly be overcome.

#### The Future of Microservices

After looking at both the advantages and the overheads associated with MSA, we now need to know about what is the current scenario in Microservices and what the future could be like. During this time, there are a lot of organizations and private companies such as Netflix, Amazon, Google, etc. that are leveraging this Architecture and a lot others are shifting towards this. With the advent of Internet of Things and increased requirement for communication between the different services coupled with the complexity of development, MSA could probably have a very bright future ahead. New projects are starting to show up on the web helping users migrate to MSA and to help the developers overcome the operational overhead related to the development and deployment of Microservices.

#### Resources to look at

Found the read interesting or intrigued by the MSA? Here are a few references you can look forward to for knowing more about Microservices:

  * <a href="https://smartbear.com/learn/api-design/what-are-microservices/" target="_blank">What are microservices?</a>
  * <a href="http://microservices.io/articles/whoisusingmicroservices.html" target="_blank">Who is using Microservices?</a>
  * <a href="http://eventuate.io/exampleapps.html" target="_blank">Example Microservices (Code Based Examples)</a>
  * <a href="http://karaf.apache.org/" target="_blank">Apache Karaf Project</a>