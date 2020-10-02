---
id: 194
title: 'Project Bolt: Automating Large Scale Performance and Scale'
date: 2017-11-29T17:22:54+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2017/11/29/193-revision-v1/
permalink: /2017/11/29/193-revision-v1/
---
So, it has been a long time since I have written something over here now. So, what all have I been doing in this duration, that is something, which I plan to cover with this post.

In my role of working as an Performance engineer, there are some really interesting challenges to deal with. These not only include trying to understand the software architectures and tracing the issues which impact the performance of the software, but also some really nice challenges to managing the large infrastructures that are present behind the scenes while doing performance and scale testing of the software. So, what all are these challenges, let&#8217;s see:

## Challenges while doing Performance and Scale Testing

Performance and Scale testing is a serious stuff, which requires a lot of coordination between the different tasks. Once you have missed a single step, you should start preparing for a complete re-run of the whole test because, you might just have missed a very important metric.

Beyond this, coordinating the runs of a single task on a very large number of machines poses another challenge. This happens due to several reasons, such as, network slowness, malfunctioning machines, high overhead of transmitting the task data, etc. This becomes more grave, if some tasks are required to run simultaneously on multiple machines and the final results can differ a lot if this simultaneous execution of tasks is not maintained.

Now, another challenge, which is likely the first basic challenge that we pose while running the performance and scale experiments is, provisioning our environment. This provisioning involves configuring the operating systems (opening firewall ports, configuring SElinux, etc), installing the required softwares, setting up monitoring, etc. Well, this might not look as serious as an issue, when you have to do all this on 4-5 hosts, but imagine, what if you have to do this on hundreds to thousands of different hosts. No, manual provisioning is just not done for such a scale.

## Mitigating over the challenges

So, the situation is not as bad as it seems. Over the period of time, we have came up with different solutions that can help reduce the impact of our problems. These include, using tools like Ansible to automate the provisioning of a large number of hosts.

When we say we use Ansible, I mean, we use it extensively. We have written complete tools written on top of Ansible, which are responsible for configuring our whole infrastructure using an Agentless approach. With Ansible and its SSH based mechanism to connect to the hosts, we have reduced our environment configuration time considerably.

So, what all do we do with Ansible right now?

Well, we use Ansible to install new packages, configure operating system settings such as Network ports, SELinux policy, firewall, etc. The other tasks include, installing and configuring docker and the filesystems, creating docker container images, and then launching thousands of containers over a set of more than 30-40 bare metal servers. The task gets more inclusive when we are also creating new virtual machines and installing operating system over them, all using Ansible.

Once we have the environment setup done, we also run some of our performance and scale tests using Ansible.

So, we have done a lot using Ansible, but where is the problem then?

Well, Ansible uses SSH and establishing SSH connections to a number of hosts involves some considerable amount of overhead. This overhead includes, the overhead of establishing a SSH connection to the remote host and then some extra resource usage. Things become a bit more serious, when we need to establish a connection to 1000+ hosts and we require the tasks to be executed simultaneously over all these hosts with some required degree of precision.

Over the period of time, we have been experimenting a lot with Ansible and some other tools and what we came up as a solution to our problems was, we will require a different tool, which can help us manage our performance and scale experiments at a large scale while providing us a considerable amount of customization in terms of the way we use the tool. The tool should not be intrusive but should rather integrate well into our day to day life.

## Introducing Project Bolt

After facing several problems during our Performance and Scale experiments, the goals with Project Bolt[1] were pretty much defined, we wanted to build a system which can help us automate our Performance and Scale tests in a large environment. Let&#8217;s look at some of the goals which Project Bolt targets:

  * **Provisioning large scale infrastructure**: Provisioning the infrastructure is the first step that comes into the performance and scale testing lifecycle. One of the major goals with the project was that, we should be able to provision our infrastructure with high concurrency and this should be done automatically for every new run.
  * **Providing a quick mechanism to transmit data to large number of hosts**: When we have to run performance and scale tests on a large number of systems, we need a mechanism that can quickly dispatch the data to a large number of hosts.
  * **Running tasks concurrently on a large number of systems**: During some of the performance and scale experiments, we need to generate a real life load by making requests concurrently from a large number of systems. This requires a lot of precision in terms of the fact that, all the hosts should execute the task at the same time.
  * **Validating whether all the hosts are healthy or not**: What good will be an environment, if the hosts are misbehaving. We needed a mechanism through which we can know, if some of the hosts are experiencing some issues or not.
  * **Gathering the metrics of the task runs from the hosts**: After the tasks has run, we need to gather the system metrics from them. This includes, how much memory was consumed, CPU usage, network IO and much more. Plus, the type of metrics which needs to be collected should also be configurable.
  * **Low overhead**: Well, our tools should be able to get the data from the system without generating significant load of their own while running. This is important, because the results might become false positives if our orchestration systems consumes significant system resources and their is a contention of resources between the actual tasks and our own tools.

So, how are we trying to achieve these goals? Although the project is still under heavy development and only some of the goals have been realized. Let me spend some moments trying to expand about the architecture of the project in a brief.

## Project Bolt Architecture

While I will keep a deep dive of the architecture of the whole project as a separate post, I will try to cover the project architecture in a brief here.

Majorly, Project Bolt consists of 3 main components:

  * **Bolt Server**: The bolt server is the central component in the project, which is responsible for the dispatch of the requests to all the connected systems. The server takes care of the parsing of the job tasks and then schedules the run of the tasks on the connected client systems. This scheduling also includes managing of dependencies in case when one task depends on the execution status of the other task. Once everything is schedules, the bolt server is responsible for the dispatch of the task to the client systems and making sure that the tasks occur at the specified time. The functionality provided by the bolt server includes: 
      * Parsing of the task files
      * Scheduling and executing the tasks on the client systems
      * Keeping a check on the status of the running tasks
      * Keeping a check on the health of the client systems
  * **Bolt client**: Every task node inside the infrastructure runs a component known as bolt-client. This client is responsible for establishing a connection to the bolt-server, and hence also providing bolt-server with an identification of the system which should execute the tasks. The bolt-client receives the commands from the bolt-server and executes the tasks accordingly. While the tasks are being executed, bolt-client is also responsible for the collection of the host metrics where the task is being executed. So, some of the functionality provided by the bolt-client include: 
      * Connecting and identifying the task execution system to the bolt server
      * Executing the tasks on the host sent by the bolt-server
      * Collecting the host system metrics while running the task
      * Reporting of the metric data to the bolt-sink
  * **Bolt Sink**: The sink acts as a central endpoint where all the clients report the results of the task execution. These results include the status of the task execution on the client (e.g. finished, error, warning) as well as the metrics that were collected from the system. The sink is then responsible for aggregating the data from all the hosts and providing a visual appearance to the raw data. Sink also provides a feedback loop to the server through which the final status of the task is communicated to the server. This is important because, if the server does not know about the final status of the task which was sent for execution, it will never be able to schedule the execution for the next task. So, the sink provides us with the following functionality: 
      * Collection and aggregation of data from multiple hosts
      * Providing a visual appearance to the collected data
      * Reporting the overall status of the task to the Bolt server

One of the major challenges while architecting the system was that, we had a lot of components which needs to be enabled or disabled on the runtime, and this process should not require the restart of the whole system. To tackle this issue, we utilized a dynamic way to handle the components. This includes a mechanism to monitor if there is a new component that has been plugged in or out of the system and loads/unloads it accordingly. We know, with such kind of dynamic behavior, things can go a bit tricky, if some component which does not follow a particular set of rules is plugged in. Well, such issues are quite hard to take care completely, but with Bolt, we have tried our best to keep the system as stable as possible. This is made possible by keeping checks on what is loaded into the bolt and what is being unloaded. Also, these dynamically pluggable components run in their own threads hence keeping the main process stable unless there is a fatal error inside the core system only.

### So, what is the current status?

Project bolt is still under heavy development, with all the core functionality already up and running whereas some other parts essential to provide the complete experience still under development. Although the release is functional and can do quite some tasks even now, we still have a long way to go and are expecting some more time after which we will have a solid system in place to help us during the Performance and Scale runs for the different projects.

If you want to take a look at the current codebase, feel free to look over at our Github organization[1] which groups all the different components under a common hood.

[1] https://github.com/project-bolt