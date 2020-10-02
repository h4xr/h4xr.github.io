---
id: 134
title: Building a Microservices Registry Server
date: 2016-11-19T11:55:13+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2016/11/19/131-revision-v1/
permalink: /2016/11/19/131-revision-v1/
---
Microservices nowadays are gaining a lot of momentum, all due to the case that it helps ease the development of the application by breaking the application into parts such that each part specializes in a particular task. This separation helps the developers in building the application component by component and launching them as they are ready to be shipped.

Though, the application development efforts are eased through Microservices, issues for devops increase. Managing the growing number of Microservices, abstracting the internal interfaces and routing the traffic properly to the correct service. To help overcome the issues, we have the concepts of API Gateways and Service Registries. Where the API Gateways can help in providing abstraction to internal interfaces consisting of number of Services built upon different protocols, the Service registry helps in maintaining the record of which Service is running and where it is running, what is the current status of the service.

The Service Registry can be leveraged by API Gateways, to route requests to the correct service by locating where they are running (host and port). Another case, where the service registry can be used is, to automatically spawn services as required and maintain the record of where they are running.

Recently, I took upon a challenge for myself to build a Microservice Registry Server. The aim with this was to understand, how the service registries are created, how the individual Microservices can communicate with the Registry Server to register and unregister as they go live or stop respectively and how the API Gateways can communicate with the registry to find the correct location for where to route the request.

To build this Microservice Registry Server, a considerable amount of time was spent reading a number of different articles and publications explaining how the API Gateways work, researching about the best way through which communication can be established between the different services and between the Registry Server and how to implement a reliable interaction between the registry server and the applications that want to communicate with the registry server.

<img class="aligncenter size-full wp-image-132" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?fit=640%2C157" alt="Microservice API Registry" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?w=1239 1239w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=300%2C74 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=768%2C188 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=1024%2C251 1024w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

#### **Microservice Registry Server Architecture**

&nbsp;