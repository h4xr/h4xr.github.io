---
id: 87
title: Using strace for tracing System Calls
date: 2016-08-05T13:49:30+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=87
permalink: /2016/08/05/using-strace-for-tracing-system-calls/
image: /wp-content/uploads/2016/08/strace.png
categories:
  - Debugging
tags:
  - analysis
  - debugging
  - linux
  - strace
  - system calls
---
There are times when we need to trace what the program is doing. To know about what calls our program makes and what signals it raises or receives. The _strace_ command can prove to be useful when we are tracing a program for which we don&#8217;t have the source code for, or for one we don&#8217;t want to run _gdb_. We can use the tool to analyze where the program hangs, or which calls by the program are failing, resulting into an erroneous output. So, let&#8217;s take a look at how can we use strace.

#### A simple example of using _strace_

strace can be invoked using the strace command in the terminal.

`$ strace`

Now, let us look at a small example of using strace, let&#8217;s trace the system calls for _ls_ command.

`$ strace ls`

Here is the sample output:

<img class="aligncenter size-full wp-image-88" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?fit=640%2C390" alt="strace" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?w=1439 1439w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?resize=300%2C183 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?resize=768%2C469 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?resize=1024%2C625 1024w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

The above image shows all the system calls that were made while generating the output of the ls command. This output contains the name of the system call, what parameters were passed to the call and what was its return status. This can provide a lot of help in debugging the unexpected behavior of the program.

#### Attaching _strace_ to a running process

_strace_ can also be attached to a running process and can be used to record its system calls. To do this, we must know the PID of the process which we want to debug. We can get this PID using the _ps_ command. When we have the PID, we can pass it to strace using the -p parameter to the command. Let us take a look, how we can do it:

`$ strace -p 4724`

<img class="aligncenter size-full wp-image-89" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?fit=640%2C390" alt="strace firefox" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?w=1437 1437w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?resize=300%2C183 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?resize=768%2C468 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?resize=1024%2C624 1024w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-firefox.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

So, here we see, the output of strace while tracing the firefox process being run by the system. This can be used to analyze the behavior of the process and what it is doing currently.

#### Getting the execution time for system calls

Sometimes, it is important to understand which system call is taking how much time. This knowledge can be used to identify the bottlenecks in the application. strace can be used to return the execution time for the system calls by passing a simple -T parameter to the command. Let&#8217;s take a look:

`$ strace -T ls`

<img class="aligncenter size-full wp-image-90" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?fit=640%2C388" alt="strace exec time" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?w=1438 1438w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?resize=300%2C182 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?resize=768%2C465 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?resize=1024%2C620 1024w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-exec-time.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

As we can see, the command is showing the time it took for a particular system call to complete(highlighted part). Use this information to identify where the performance lags.

#### Generate statistics for System Calls

Sometimes, we only want to look at which system calls were made by the program and how many system calls of same type were made. These statistics can be used to identify the most commonly used system calls by the program. The system call statistics can be generated by passing the -c parameter to the command, like:

`$ strace -c ls`

<img class="aligncenter size-full wp-image-91" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?fit=640%2C234" alt="strace stats" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?w=1439 1439w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?resize=300%2C110 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?resize=768%2C281 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?resize=1024%2C374 1024w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-stats.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

As we can see, the command displayed us the statistics about each and every system calls. This information contains the name of the system calls, how much time it took to complete the execution of the system call, number of times the call was made and how many errors the call encountered.

#### Output data to a file

Viewing data in the terminal is one thing, but sometimes we need to record the data to an file so we can analyze it later. Passing the -o parameter followed by the name of the output file to the _strace_, we can save the data to an external file.

`$ strace -o trace.txt ls`

<img class="aligncenter size-full wp-image-92" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?fit=640%2C387" alt="strace file" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?w=1437 1437w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?resize=300%2C181 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?resize=768%2C464 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?resize=1024%2C619 1024w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/strace-file.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

So, here we can see that the data of strace was written to an external file named trace.txt.

This was an overview of the _strace_ command, which we can use to debug our programs or to analyze the system calls made by an application. There are a lot of other options that are provided by the _strace_ command which can be used to format the output or to control the verbosity level of the output being displayed by the command. Go ahead and try the command to analyze the applications.