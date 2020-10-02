---
id: 56
title: Managing development environment with vagrant
date: 2016-07-19T05:48:43+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2016/07/19/55-revision-v1/
permalink: /2016/07/19/55-revision-v1/
---
While developing or building a particular software program, we are always required to setup the tools and frameworks needed to build the project. For 1 or 2 projects the task is simple, but the problem rises when working with more projects. Being a developer myself who works on different projects, recently I also bumped into the problem of setting up my environment for building the project. The project I was working on was the Servo browser engine by Mozilla. While setting up the system, I faced the problem of having conflicting dependencies, where after installing one library, the X-Server failed to start from the next system reboot. Although, I was able to fix the issue, but, it was a high time for me to think of something different because, I can&#8217;t afford these type of errors. I guess, many of you could have also faced these errors.

After some searching and exploring different solutions, the one I settled with was Vagrant. So what is Vagrant exactly,

Vagrant is a tool for building and distributing development environment. By this, you can be sure that all your developers are using the same package stack for development as you are. Setting up Vagrant is pretty straightforward. All you have to do is to find a suitable base box which you can customize according to your own needs. Distributing your development environment is also pretty easy, just setup a bootstrap file which is used to provision your system with the necessary packages before the first boot.

Vagrant is not a virtual machine in itself, it uses other virtual machine systems like VirtualBox, etc. as providers and run the system in them.

So, what I did for building the Servo project was, I decided to build my own vagrant configuration which can be used to work with the project. The configuration uses a Ubuntu 14.04 system as the base system and installs the required packages over it and then syncs the servo source code from the GIT. By setting this up, I now feel relaxed about working with different projects on my system and not to worry about system instability after installing the dependencies for one project.

Here are some of the references you might find useful:

  1. <a href="http://www.vagrantup.com" target="_blank">vagrant by HashiCorp</a>
  2. <a href="https://www.vagrantup.com/docs/getting-started/" target="_blank">Getting Started with Vagrant</a>
  3. <a href="https://github.com/h4xr/servo-build" target="_blank">Vagrant configuration for Building Servo</a>