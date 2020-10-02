---
id: 65
title: Performance profiling with perf command
date: 2016-07-31T19:34:00+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=65
permalink: /2016/07/31/performance-profiling-with-perf-command/
categories:
  - Optimization
tags:
  - linux
  - optimizations
  - perf
  - performance
  - profiling
  - tools
---
Performance of any program plays an important role, it is something which makes a user stay with your software. Imagine, if your software takes minutes to start even on a power machine, or show visible performance drops when doing some important work, that will be bad. Things such as operating system kernel are even more performance critical, because if they lag, the whole system will lag. As a developer, it&#8217;s our responsibility to write code, that provides the highest possible performance.

Now to write programs that provides a good performance, we should know which part of our program is becoming a bottleneck, so that we can focus our efforts on optimizing that region of our code. There are a lot of tools that are out there which can help you as a developer to profile your program and to better understand which part of your code needs some attention. So, for this article lets discuss one of the tools which can help you to profile your program on Linux.

### Introducing _perf_ command

The perf command in linux gives you the access to various tools which are integrated in the Linux kernel since version 2.6. These tools can help you to collect and analyze the data about the performance of your program or system. The perf profiler is very fast and lightweight and really precise. So, without wasting much time, lets get going with the tool.

To use the perf command, you will be requiring the linux-tools package to be installed on your distro. Once you have the required package installed, fire up your terminal and execute the perf command. You will get an output similar to something shown below

<img class="aligncenter wp-image-66" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-command.png?resize=512%2C327" alt="perf command" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-command.png?resize=300%2C192 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-command.png?resize=768%2C491 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-command.png?w=839 839w" sizes="(max-width: 512px) 100vw, 512px" data-recalc-dims="1" /> 

The perf command gives a lot of options you can use to profile your code. Let&#8217;s go through some of the commands which can come to our rescue frequently.

#### Listing the events

`perf list`

The list command will show the list of events which can be traced by the perf command. The output will look something like the one given below:

<img class="aligncenter wp-image-67" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-events.png?resize=487%2C307" alt="perf events" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-events.png?resize=300%2C189 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-events.png?resize=768%2C484 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-events.png?w=844 844w" sizes="(max-width: 487px) 100vw, 487px" data-recalc-dims="1" /> 

There are a lot of events, that can be traced via the perf command. Broadly these events can be software events such as context-switches, page faults, etc. or hardware events that originate from the processor itself, like, L1 cache misses, number of clock cycles, etc.

#### Counting events with perf stat

The perf stat command can be used to count the events related to a particular process or command. Let&#8217;s look at a sample of the use case by executing the following command:

`perf stat -B dd if=/dev/urandom of=/dev/null count=50k`

The output of the command will list the counters associated with different types of events that occurred during the execution of the above command.

To get the system wide statistics for all the cores on your system, you can go and execute the following command:

`perf stat -a`

The command will collect and report all the event statistics that were collected until you press Ctrl+C.

The stat command gives you the option to select only specific events which should be accounted for. For selecting the events, you can execute the stat command with the &#8216;-e&#8217; option followed by the comma separated list of events that you want to account for. Example,

perf stat -e cycles,page-faults -a

This will provide statistics about the events named cycles and page-faults for all the cores on the system

To get the stats for a specific process, you can execute the following command

`perf stat -p <PID><br />
`  
where PID is the process id of the process you want the stats for.

#### Sampling with perf record

The perf record command is used to sample the data of events to a file. The command operates in a per-thread mode and by default records the data for the cycles event. For example, to collect the system wide data for all the CPUs execute the following command:

`perf record -a`

The record will collect the data for samples until you press Ctrl+C.

The data collected by the perf record is stored in a file name perf.data by default. If you want to store the data in some other file, you can pass the name of the file to the command using the &#8216;-o&#8217; option.

To see the recorded data, execute the following command:

`perf report`

Executing this command will bring up a screen which looks somewhat similar to the following:

<img class="aligncenter size-full wp-image-68" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-report.png?fit=640%2C387" alt="perf report" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-report.png?w=847 847w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-report.png?resize=300%2C181 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-report.png?resize=768%2C464 768w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

&nbsp;

The report contains 4 columns, which have their own specific meaning:

Column 1 (Overhead): It indicates the percentage of overall samples collected in the corresponding function.

Column 2 (Command): The command to which the samples belong

Column 3 (Shared object): It shows the name of the image from where the samples came from.

Column 4 (Symbol): This column shows the symbol name which constitutes the sample as well as the privilege level at which the sample was taken. there are 5 privilege levels indicated as

[.] User level

[k] Kernel Level

[g] Guest kernel level (Visualization)

[u] Guest OS userspace

[H] Hypervisor

If made to run for your program, the command will help you display the most costly functions which you can then focus upon for optimizations. For the same, lets take the example of firefox process, and sample the data for it.

<img class="aligncenter  wp-image-69" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-firefox.png?resize=457%2C279" alt="perf firefox" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-firefox.png?resize=300%2C183 300w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-firefox.png?resize=768%2C469 768w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/perf-firefox.png?w=844 844w" sizes="(max-width: 457px) 100vw, 457px" data-recalc-dims="1" /> 

Executing the perf report command, I get a screen like this listing the various symbols in the decreasing order of their overhead

<img class="aligncenter size-full wp-image-70" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Sperf-cycles.png?fit=640%2C386" alt="Sperf cycles" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Sperf-cycles.png?w=850 850w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Sperf-cycles.png?resize=300%2C181 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Sperf-cycles.png?resize=768%2C463 768w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Now, with this data we can identify the functions which are generating the highest overhead in our code and start our journey to optimize them for the performance.

So, this was a small introduction of using perf command to profile programs and system for the performance. The perf command has lots of other options which gives you the power to run a few benchmarks on the system as well as to annotate the code. For further information on the perf command you can visit <a href="https://perf.wiki.kernel.org/index.php/Main_Page" target="_blank">Perf wiki</a>.