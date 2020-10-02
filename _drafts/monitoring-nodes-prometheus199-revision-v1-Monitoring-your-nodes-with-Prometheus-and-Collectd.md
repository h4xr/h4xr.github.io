---
id: 200
title: Monitoring your nodes with Prometheus and Collectd
date: 2018-02-13T12:49:44+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2018/02/13/199-revision-v1/
permalink: /2018/02/13/199-revision-v1/
---
System and application monitoring is a hot topic nowadays, and why it won&#8217;t be. Those small numbers which you collect from your system and applications, go a long way into explaining the cause for a lot of problems. There are a number of tools that are available which helps us achieve our goals of monitoring our systems and gathering the metrics from them. When you have so many choices available, it becomes hard to choose, which tool to go for.

While working in Performance and Scale Engineering team at Red Hat, we have been experimenting a lot with the different kind of tools that are available for monitoring the systems and applications and gathering the collected metrics from them. This post aims to document, how we can use a combination of Prometheus and Collectd to monitor our systems.

### The advantages of going the Prometheus route

Using prometheus as a monitoring system provides with several advantages, some of them are:

  * Prometheus provides a complete package for storing, analyzing and alerting on the collected metrics
  * The Prometheus data model allows someone to drill down to individual instances
  * It proves to be useful for storing data that is collected at irregular intervals of time
  * Allowing us to setup a HA architecture running Prometheus

Prometheus also provides us with an in-built dashboard which can be used to take a look at the metrics being collected and also to graph them. The addition of a simple query language adds a cherry on top of the cake.