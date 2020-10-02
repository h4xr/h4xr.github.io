---
id: 131
title: 'Building a Microservices Registry Server (Part 1: The Architecture)'
date: 2016-11-19T12:36:18+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=131
permalink: /2016/11/19/building-microservices-registry-server-part-1-architecture/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"98b9ec9ca353";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:102:"https://medium.com/@h4xr/building-a-microservices-registry-server-part-1-the-architecture-98b9ec9ca353";}'
image: /wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png
categories:
  - Projects
tags:
  - API Gateways
  - Development Effort
  - Microservices
  - Open Source
  - project
  - Registry Server
---
Microservices nowadays are gaining a lot of momentum, all due to the case that it helps ease the development of the application by breaking the application into parts such that each part specializes in a particular task. This separation helps the developers in building the application component by component and launching them as they are ready to be shipped.

Though, the application development efforts are eased through Microservices, issues for devops increase. Managing the growing number of Microservices, abstracting the internal interfaces and routing the traffic properly to the correct service. To help overcome the issues, we have the concepts of API Gateways and Service Registries. Where the API Gateways can help in providing abstraction to internal interfaces consisting of number of Services built upon different protocols, the Service registry helps in maintaining the record of which Service is running and where it is running, what is the current status of the service.

The Service Registry can be leveraged by API Gateways, to route requests to the correct service by locating where they are running (host and port). Another case, where the service registry can be used is, to automatically spawn services as required and maintain the record of where they are running.

Recently, I took upon a challenge for myself to build a Microservice Registry Server. The aim with this was to understand, how the service registries are created, how the individual Microservices can communicate with the Registry Server to register and unregister as they go live or stop respectively and how the API Gateways can communicate with the registry to find the correct location for where to route the request.

To build this Microservice Registry Server, a considerable amount of time was spent reading a number of different articles and publications explaining how the API Gateways work, researching about the best way through which communication can be established between the different services and between the Registry Server and how to implement a reliable interaction between the registry server and the applications that want to communicate with the registry server.

<img class="aligncenter size-full wp-image-132" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?fit=640%2C157" alt="Microservice API Registry" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?w=1239 1239w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=300%2C74 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=768%2C188 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/11/Screenshot-from-2016-11-19-17-04-10.png?resize=1024%2C251 1024w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

#### **Microservice Registry Server Architecture**

So, while building the Microservices Registry Server, there were a few goals that had the utmost priority, some of these were:

  * Modular Architecture
  * Simple communication between different parts of the Registry Server
  * Easy interfacing between Microservices and the Server

Keeping the above in mind, the Registry Server consists of various components, working together to bring the project alive. These components include:

  * **XMLRPC Server:** To allow different services to register with the server, they need to access the API exposed by the server. During the development, I had a choice of choosing whether I should go and take the path of RESTful API that can be exposed by the Server or go some other way around. After going through a lot of option, I settled with the fact of using Remote Procedure Calls through which the applications can interact with the Server to register and unregister themselves. To implement this, I went ahead with using a simple XMLRPC Server that will listen to the requests and do the necessary action over them.
  * **MicroDB:** MicroDB is an in-memory database, leveraging the functionality of python lists and dictionaries. MicroDB is a small serverless, in-application embeddable database, that can be used efficiently to store a small number of records. MicroDB provides a JSON based API that can be used to interact with the Database.
  * **ServiceDB:** ServiceDB is a data store built on top of the MicroDB for storing the Service Records. The ServiceDB provides a simple API that can be used by the applications and plugins to register or unregister a Service from the database. The other functionalities provided by the ServiceDB include, querying the service information, updating the service status and traversing the service records.

#### **The XML-RPC API**

The service register exposes a simple XML-RPC based API, that can be leveraged by the Microservices to register and unregister themselves from the Service Registry. Some of the methods exposed by the XML-RPC API include:

  * _**register()**:_ The register method, takes in the name of the service, the information about where it is running (host and port), the service status and registers the service with the ServiceDB. Once registered with the ServiceDB, the consuming applications can query the ServiceDB for finding the available services and where they are running.
  * _**update_status()**_: The method takes in the name of the service and the new status value and updates the status in the ServiceDB such that it is reflected to all the consuming applications. The service status are a predefined set of values that are defined inside the ServiceDB.
  * _**unregister()**_: The method, takes in the name of the service that has already been registered with the ServiceDB, and is used to unregister the service from the database.

#### **Resources**

[1] <a href="https://github.com/h4xr/MicroReg/" target="_blank">Microservices Registry Server</a>

[2] <a href="http://microservices.io/patterns/apigateway.html" target="_blank">API Gateway Pattern</a>

[3] <a href="https://docs.python.org/2/library/xmlrpclib.html" target="_blank">Python xmlrpclib</a>