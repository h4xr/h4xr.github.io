---
id: 140
title: 'Building A Microservices Registry Server (Part 2: The API Gateway)'
date: 2016-12-01T07:12:12+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=140
permalink: /2016/12/01/building-microservices-registry-server-part-2-api-gateway/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"36fb1b3f3ebf";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:101:"https://medium.com/@h4xr/building-a-microservices-registry-server-part-2-the-api-gateway-36fb1b3f3ebf";}'
image: /wp-content/uploads/2016/12/symbol.png
categories:
  - Automation
  - Projects
tags:
  - API Gateways
  - deployment
  - Load Balancing
  - MicroReg
  - Microservices
---
So, I have recently been on a journey to understand the Microservices architecture and building tools that can help in deployment and management of Microservices. During this, I along with a team of some awesome developers went on in a hackathon, where we brainstormed on the idea and tried to expand it. Continuing on the idea of a Microservices registry server as explained in <a href="http://saurabhbadhwar.xyz/blog/2016/11/19/building-microservices-registry-server-part-1-architecture/" target="_blank">Part 1</a> of the series, we started to build an API Gateway. Just think, what use would a Service Registry have been, if there is no application consuming the data.

So, what exactly on this earth an API Gateway is?

#### What is an API Gateway

An API Gateway is an implementation, that acts as a single entry point to your application. Some of the tasks that are performed by an API Gateway include:

  * **Request Routing**: When a request arrives at the API Gateway, the gateway is responsible for forwarding it to the appropriate Microservice.
  * **Composition**: A single end-point on API Gateway can invoke multiple Microservices to generate the response.
  * **Protocol Translation**: There might be a chance that the Microservices are based on different protocols, the API Gateway is responsible for translating the client requests to the optimal protocol that the Microservice can handle.

The advantage of implementing an API gateway are enormous. These may include:

  * Offloading the API requests from client to server
  * Better management of different protocols
  * Ease of modification
  * Increased robustness in case of service failure
  * Providing custom end-points for different devices

While designing our API Gateway solution, we kept the above points in mind and added a few more. So, what did we build?

#### Our Solution

So, while designing our solution, one of the primary aim was, it should efficiently use our Registry server. So, with that in mind, the first thing we implemented was, a proper way through which the gateway can communicate with the server. This was made possible by the use of server client library. The client library is what makes the cross application communication ease. For the communication, we effectively use the XML-RPC protocol.

So, what our solution is capable of:

  * **Dynamic Request Rerouting**: When the request is made to the gateway, it find the Microservices that can handle the request. Then, the gateway communicates with the server to find, where the Microservice is running and effectively re-routes the request to the appropriate Microservice.
  * **Load Balancing**: The requests being routed by the API gateway are assigned to services in a round robin manner, so as to balance the load among the services.
  * **Robustness:** Failures can happen anytime. Keeping this in mind, we need to take care that our application never fails. The gateway, is able to provide cached answers in case the Microservice cannot be reached.
  * **Flexibility**: Our solution provides flexibility. This is done by dynamically reloading the API end-point table when a new endpoint is added to the API gateway.

This was one of the solution that we hacked upon during the course of the hackathon. Well, we won too 🙂

Want to see, what we built? Head over to the repository at <a href="https://github.com/MicroReg/microreg-gateway" target="_blank">Github</a>