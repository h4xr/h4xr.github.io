---
id: 84
title: 'System Performance: Understanding those Metrics'
date: 2016-08-04T04:43:40+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2016/08/04/80-revision-v1/
permalink: /2016/08/04/80-revision-v1/
---
Building up on my previous post, where I talked about profiling applications using perf command. Now, I will like to shed some light upon some other tools which can come handy in understanding your system performance. Linux provides a lot of inbuilt tools which can help you understand how your system is behaving and can also help you identify the performance bottlenecks. These inbuilt tools provides you with different kinds of metrics which reflect your systems performance, but what these metrics exactly signify? Let&#8217;s take a look.

#### Load Averages

The first and the most basic metric that we can take a look upon are the load averages. To see the load averages, execute the following command on your terminal:

`uptime`

The output of the command will look something like the one shown below:

<img class="aligncenter size-full wp-image-81" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Load-averages.png?fit=640%2C80" alt="Uptime output" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Load-averages.png?w=731 731w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Load-averages.png?resize=300%2C37 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

The command listed the current value of uptime, the no. of user sessions and the load averages. Let&#8217;s focus on the load average output of the command.

As we can see, the load average shows 3 comma separated values _0.43, 0.49, 0.60._ These values correspond to the load averages for 1 minute, 5 minutes and 15 minutes respectively. Now, what does these load averages signify. On linux, these load averages denote the number of processes that want to run. This includes the processes that want to run on CPU and those processes which are blocked in uninterruptible I/O. If these load averages are higher than the core count on your system, then the CPUs are saturated i.e. there are a high number of processes that are waiting for the CPU time.

#### Swap-ins and Swap-outs

The second important metric that we can look upon is the state of our virtual memory. First let us take a look at what these metrics look like, and then we will go a bit into what they mean.

To look at the virtual memory(VM) statistics, run the following command in your terminal

`vmstat 1`

This command will display the VM stats at an interval of 1 second. The output should look something like this:

<img class="aligncenter size-full wp-image-82" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/VM-stats.png?fit=640%2C229" alt="VM stats" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/VM-stats.png?w=727 727w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/VM-stats.png?resize=300%2C107 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

The command gave a fair amount of data related to our Virtual memory. Now, let us take a look at what we can infer from this.

Memory(free): This gives the information about how much free memory is currently available for us. The more the value, the more is the space for new processes to run.

Swap(si and so): This gives the information about the swap. The swap comes in when the system is going out of memory. If the value of si and so is anything above zero, your system is running out of memory.

CPU Time: The fields us, sy, id, wa, st shows the breakdown of the CPU times. This information is necessary to know where the system was busy. This information contains the user time(time running user processes), system time(time running the kernel threads), idle time(the time for which CPU was sittling idle), waiting time(the time for which the system was waiting for I/O to complete) and the stolen time(the time used by hypervisors).

#### I/O Statistics

I/O is an important metric to look upon at. The CPUs are fast whereas the I/O devices are slow, this generates a big bottleneck in performance when the applications are waiting for the I/O to complete. During this time, the CPU might be just sitting idle, waiting for the application to complete its I/O so that it can be serviced. To get the I/O statistics about the system, we can run the iostat command. Running the following command will display the data for your IO devices.

`iostat -xz 1`

The output of the command will look something like the one shown below:

<img class="aligncenter size-full wp-image-83" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/iostat.png?fit=640%2C171" alt="iostat" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/iostat.png?w=1109 1109w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/iostat.png?resize=300%2C80 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/iostat.png?resize=768%2C206 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/iostat.png?resize=1024%2C274 1024w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

The above image displays the information returned by the iostat command. This information includes the number of reads and writes happening on the device in kB/s as well as the wait times and utilization of the device.

rkB/s and wkB/s: This denotes the amount of data being read and written for the particular I/O device in kB/s.

await: This tells the average time the application performing the I/O is suffering when accessing the particular device. This time includes the time that the application spends in the waiting state and the time that the application spends performing the specified operation. If these times are more than what you expect from the hardware, then it might be the fact that device is either being used very frequently by a lot of processes or there is some malfunctioning.

%util: This shows the utilization percentage of the device. The higher the values, the more the device is being used. A 100% utilization indicates device saturation.

So, these are a few things which I learned while trying to understand how to understand those different metrics indicative of the system performance. These metrics can be helpful in identifying what is causing the system to behave slow or why a process is taking a lot of time to start. There are a lot of excellent articles over the net that cover these topics and gives a deeper insight to individual commands which should a must to read about if you are interested in understanding your system performance.

&nbsp;