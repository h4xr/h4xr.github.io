---
id: 61
title: My Journey into the world of Linux Kernel
date: 2016-07-30T11:30:23+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=61
permalink: /2016/07/30/my-journey-into-the-world-of-linux-kernel/
image: /wp-content/uploads/2016/07/logo-linux.png
categories:
  - Uncategorized
tags:
  - data structures
  - Linux Kernel
  - Open Source
  - Operating systems
---
Knowing how a thing functions internally is a great benefit, while understanding its functioning you get to know a lot about how a thing was built, what design philosophies were used and finally how everything was implemented. I have always been interested in knowing how things function internally, how they are built and implemented. Operating Systems is one field which have always fascinated me. How a particular piece of software forms the heart of everything we do on our computers &#8211; from running multiple programs to managing the hardware resources. This fascination of mine made me curious to know more about how Operating Systems work. During the last few years, I have been visiting various forums over the Internet like <a href="http://wiki.osdev.org" target="_blank">osdev wiki</a> and reading books about the OS Theory. Over the time, I have used various Operating Systems, some for primary use and others, well, just to test. Since last 2 years I have been a user of one or another distro powered by the Linux Kernel. The more I have used these Distros, the more I have came to admire the Linux project. The sheer amount of control that resides in the hand of user is something amazing and not found in other operating systems. Whether I want to change the desktop environment or something which governs the CPU scheduling in the OS, Linux gives me the power to do so. Being a person, who is always curious to know more about how Operating Systems work, I naturally developed the feeling to explore the inner workings of the Linux Kernel.

<img class="aligncenter size-medium wp-image-62" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/logo-linux-300x149.png?fit=300%2C149" alt="logo-linux" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/logo-linux.png?resize=300%2C149 300w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/logo-linux.png?w=505 505w" sizes="(max-width: 300px) 100vw, 300px" data-recalc-dims="1" /> 

From last few days, to know more about the linux kernel and linux kernel programming, I have been following a book(<a href="https://www.amazon.com/Linux-Kernel-Development-Robert-Love/dp/0672329468" target="_blank">Linux Kernel Development by Robert Love</a>) which can clear my concepts about the internals of the kernel programming and functioning of the kernel. I must say, the journey so far has been very enriching and somewhat addictive. Over the course of days, I have got to know a lot about how the Linux kernel has been programmed, what exceptions are their in the development of the Linux kernel. There are a lot of things like the functioning of the process scheduler, allocating memory to the processes, handling the Interrupts and synchronizing various parts of the kernel that are running so as to protect from the race conditions and deadlocks that can frequently happen in the context of Operating Systems.

Reading and understanding the concepts of the kernel coupled with the explanation of the core data structures that power the kernel is a great experience in itself, there is a lot to learn about how the developers optimized a particular piece of code or how the corner cases are handled in the kernel. There certainly is no doubt that the kernel contains very high quality code, which is apparent from the reliability we see in the systems running Linux for months continuously without experiencing crashes due to kernel bugs.

The module system in Linux is also a good example of how the kernel handles the dynamic loading of new modules into the kernel space and allocating them memory from the memory pool.

There is still a lot to learn, the Linux kernel is a big project, containing code which have been written by various developers all over the world, supporting a lot of different architectures and maintaining a sufficient backward compatibility with the older releases and still being very reliable and secure. Being an open source enthusiast and a person who loves to code, I have been taking small steps to understand and write code for the Linux kernel. This, I have been doing since a few months by trying to write a few basic modules for the Linux kernel and by exploring how the patches are made and submitted to the mainline release in the kernel repository.

Wrapping up, knowing the internal functioning of the kernel is a great experience and which is somewhat addictive. You get to know a lot about how the things like memory allocation and process scheduling are managed under the hood while also getting to know about the techniques you can use to optimize the code you write for your own applications.