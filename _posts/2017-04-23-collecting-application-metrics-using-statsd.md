---
id: 176
title: Collecting Application Metrics Using Statsd
date: 2017-04-23T18:16:20+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=176
permalink: /2017/04/23/collecting-application-metrics-using-statsd/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"70d067c55158";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:81:"https://medium.com/@h4xr/collecting-application-metrics-using-statsd-70d067c55158";}'
categories:
  - Internship
  - Monitoring
  - Performance
tags:
  - Metric collection
  - monitoring
  - Performance Engineering
  - StatsD
---
Building applications and deploying them form one part of the whole game. The other part involves monitoring the system and application behaviour while the applications run. There are moments during an application lifecycle where we want to see, how much system resources are being consumed, how many files a particular process has opened and a lot more. These metrics are important as they provide us valuable insights into how the application manages scale and how well is it performing with respect to the usage of system resources.

There are a number of tools that can help us collect and visualize a varied amount of metrics and help us understand our applications better. Some of the known tools in the world of application and system metric collection and visualization include collectd, grafana, graphite, etc. This blogpost will cover a tool which you may or may not have heard off, known as Statsd.

#### What is Statsd?

What is Statsd?Statsd is a network daemon written in Node.js that runs on a system to listen and collect statistics. These statistics are sent to Statsd over network protocols such as UDP or TCP. What makes statsd a powerful statistics collection tool is the fact that, we can easily send varying types of metrics to statsd and it won&#8217;t complain. Another thing, that makes statsd a lucrative option to look forward to is its ability to interact and integrate with some of the popular tools for metric collection such as collectd, grafana, graphite, InfluxDB, etc.

The statsd project can be located at its [github repo](https://github.com/etsy/statsd)

Without wasting much time, lets start with the installation of the Statsd daemon.

#### Installing and Configuring Statsd

Since statsd is a network daemon written in Node.js, there are a few dependencies which needs to be fulfilled before we can install and run statsd. These requirements are simple and can be easily setup. So, let&#8217;s start.

The first thing to do is, to install Node.js on your system.

On Fedora(which I am using currently), Node.js can be installed by executing

<pre>sudo dnf install nodejs</pre>

Once, Node.js has been installed, the next step we need to do is to get the statsd package. Currently, the statsd package lives on its github repo and needs to be cloned before it can be run. To get the package, we can execute the following command:

<pre>git clone https://github.com/etsy/statsd statsd</pre>

Once you have cloned statsd, the next step is to create a configuration file. This configuration file will provide details about where to run our statsd server.

For the sake of this post, we can just work with the already provided exampleConfig.js configuration file, without modifying it. In this case, the statsd daemon will run on localhost(0.0.0.0) and listening to statistics over port 8125.

To start the statsd server, run the following command from inside of the statsd directory

<pre>node stats.js ./exampleConfig.js</pre>

If everything is successful, you will see an output similar to the one shown below

<img class="aligncenter size-full wp-image-178" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/04/Statsd-Running.png?fit=640%2C81" alt="" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/04/Statsd-Running.png?w=719 719w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/04/Statsd-Running.png?resize=300%2C38 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

#### Sending Metrics to StatsD

Now, we are done with the installation of the StatsD. The next step here is to send some metrics to StatsD related to our applications or system. But, before we do that, let&#8217;s see which type of metrics can be sent to StatsD.

Some of the metrics supported by StatsD include:

  * Counting (c): These are simple counters. These can be useful for keeping a count on how many times did a particular event occurred or maybe just counting the ticks on the clock 😛
  * Timing (ms): These metrics can be used to record time and can be useful to record how long did the execution of a particular operation or command took
  * Gauge (g): Gauges are arbitrary values that can be recorded by StatsD. At every flush interval of StatsD, if the gauge value is not updated, the previous value is sent.

Sending metrics to StatsD require a certain format to be followed, that is:

<metric\_name>:<metric\_value>|<metric_type>

Where,

  * metric_name: The name of the metric to be recorded
  * metric_value: The value associated with the particular metric
  * metric_type: Notation associated with the type. c for counting, ms for Timing, g for Gauge, etc.

To send a metric directly from your terminal, try executing the following command:

echo &#8220;call_count:1|c&#8221; | nc -u -w 2 0.0.0.0 8125

This command will send a new metric named call_count with value 1 and type counting to the StatsD server.

This was just a small example of how we can report a metric to StatsD. There are certain varying kind of metrics which we want to record and send to the StatsD server for recording and visualizing. For that, we may want to create scripts that automate our tasks and keep the continuity.

In the next post, I will cover, how to build SystemD service that collects some useful Application Statistics related to Apache and reports them over to StatsD. Till that, feel free to explore about StatsD and experiment with it.

Some of the resources you may be interested in:

  1. [Statsd Project](https://github.com/etsy/statsd)
  2. [Sending StatsD metrics to Graphite](https://www.digitalocean.com/community/tutorials/how-to-configure-statsd-to-collect-arbitrary-stats-for-graphite-on-ubuntu-14-04)