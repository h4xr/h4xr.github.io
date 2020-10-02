---
id: 123
title: 'Managing Microservices: A Dirty Example'
date: 2016-09-13T12:46:16+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=123
permalink: /2016/09/13/managing-microservices-dirty-example/
categories:
  - Uncategorized
---
Recently, I have been trying to understand and implement Microservices. During the last few days, the work consisted on understanding the Microservice Architecture(MSA) and to implement a few Microservices. Working on this, I came up with a super simple application based on the MSA &#8211; A To-Do application. After that, while I explored the world of Microservices, I came up with an idea, how about if the services can be launched in a manageable manner. By manageable, I mean, if the services can be started only if all the required services it depends upon are preloaded.

After some pondering over the idea, I came up with the idea of implementing such a system. Today, I have been able to implement such a system which can be used to load the Microservices. The implementation is currently capable to read a configuration file, find the services that needs to be started and then loads the service. In case, the service has some specified dependency of some other microservice which should be running, the implementation will first load the required dependencies and then start the actual microservice.

<img class="aligncenter size-full wp-image-124" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/09/Screenshot-from-2016-09-13-17-42-07.png?fit=640%2C61" alt="Loaded microservices" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/09/Screenshot-from-2016-09-13-17-42-07.png?w=722 722w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/09/Screenshot-from-2016-09-13-17-42-07.png?resize=300%2C29 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

#### Implementation Workflow

The current implementation consists of using a configuration file named _dependencies.ini_ which is read by the runner script. The runner script then goes through the dependencies section by section and then starts the service execution. During this, the services are pinged for their availability, if the services are found running, no action is taken and the execution shifts to the next service in the dependency file. If the service is not running, the runner starts the service and then moves on to the next service in the dependency list. When all the required dependencies are loaded, the runner script executes the main service.

#### Future Goals

For the current implementation, I will call it a dirty one. The code is not modular and lot of functionality is missing for the time being. In the future, I will be doing a major refactoring of the implementation and will also be adding some interesting functionality, such as:

  * Support for passing arguments and environment variables
  * Resolving circular dependencies
  * Support for Docker based containers

For experimenting with the current implementation, you can head over to [Github Repository](https://github.com/h4xr/todo) for the Microservice example and fork and run the sample code by typing  
`<br />
pip install -r requirements.txt`

python run.py

&nbsp;