---
id: 199
title: Monitoring your nodes with Prometheus
date: 2018-02-14T15:08:48+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=199
permalink: /2018/02/14/monitoring-nodes-prometheus/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"b7fdd623fbce";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:75:"https://medium.com/@h4xr/monitoring-your-nodes-with-prometheus-b7fdd623fbce";}'
categories:
  - Monitoring
  - Performance
tags:
  - Clusters
  - monitoring
  - performance
  - Prometheus
  - Scraping
---
System and application monitoring is a hot topic nowadays, and why it won&#8217;t be. Those small numbers which you collect from your system and applications, go a long way into explaining the cause for a lot of problems. There are a number of tools that are available which helps us achieve our goals of monitoring our systems and gathering the metrics from them. When you have so many choices available, it becomes hard to choose, which tool to go for.

While working in Performance and Scale Engineering team at Red Hat, we have been experimenting a lot with the different kind of tools that are available for monitoring the systems and applications and gathering the collected metrics from them. This post aims to document, how we can use Prometheus to monitor our systems.

### The advantages of going the Prometheus route

Using prometheus as a monitoring system provides with several advantages, some of them are:

  * Prometheus provides a complete package for storing, analyzing and alerting on the collected metrics
  * The Prometheus data model allows someone to drill down to individual instances
  * It proves to be useful for storing data that is collected at irregular intervals of time
  * Allowing us to setup a HA architecture running Prometheus

Prometheus also provides us with an in-built dashboard which can be used to take a look at the metrics being collected and also to graph them. The addition of a simple query language adds a cherry on top of the cake.

So, without wasting too much of time, let&#8217;s see, how we can deploy Prometheus and use it for monitoring our nodes

### Deploying Prometheus

For RHEL and any other RPM based distro, I will recommend to install Prometheus from the tar file provided on the official Prometheus website. For this post, we will be deploying Prometheus 2.1.

To setup Prometheus, first we need to get the Prometheus package. For the same, visit the Prometheus [Download page](https://prometheus.io/download/) and get the package you want to install.

Assuming, that you have downloaded the Prometheus 2.1.0 package tar file, the next thing to do, will be to extract the tar file and get all the files in a single directory

<pre>tar -xvzf prometheus-2.1.0.linux-amd64.tar.gz</pre>

Once the files are extracted, you will have the prometheus binary as well as the configuration file and a few other directories at your disposal.

Now, the next thing that we have to do is, configure Prometheus. By default, Prometheus ships with a default configuration file named as prometheus.yml. As should be evident from the extension, the configuration file uses the YAML format to specify the settings.

Quickly going through the settings, here are a few settings which is of our interest right now:

  * **scrape_interval:** This value defines, how frequently prometheus should scrape the targets for the metrics. Depending upon the number of nodes and type of the metrics you are collecting, you might want to customize this number.
  * **scrape_configs:** This section defines the settings for the targets that need to be scraped and the parameters that control how the targets are scraped

Well, now, if you just want to see Prometheus in action, go ahead and uncomment the lines under the scrape_configs setting, so, it looks like this:

<pre>scrape_configs:
  # The job name is added as a label `job=&lt;job_name&gt;` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

</pre>

This will configure Prometheus to monitor a very important(not really :P) application(itself). But, before moving to, how you can run Prometheus with this configuration file, I will like to explain a bit about what goes inside the scrape_configs section. Usually, the scrape config section will consist of the following on the minimum:

  * **job_name:** The name of the job according to which the target metric collection is grouped
  * **static_configs:** Defines the parameters about the targets that need to be scraped in a static fashion. Prometheus also supports dynamic discovery of the targets, but we are not focusing upon that parameter in this post. 
      * **targets:** It is a list of hostname:port pairs which defines the endpoints Prometheus will scrape for gathering the metrics

Now, since I have went through the basic terminology, let&#8217;s see, how to get Prometheus in action 😀

To start Prometheus, run the following command from the folder inside which Prometheus has been extracted to:

<pre>./prometheus --config.file=prometheus.yml</pre>

And&#8230;&#8230;. you are done. By now, Prometheus has been started and a web interface available to you.

Head up to your browser and navigate to the _hostname:9090_ to see the simplistic Prometheus interface.

<img class="aligncenter size-full wp-image-201" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?fit=640%2C307" alt="Prometheus Dashboard" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?w=1920 1920w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?resize=300%2C144 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?resize=768%2C369 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?resize=1024%2C492 1024w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-21-28.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Now, to see some visualizations about the data being collected, follow the below steps:

  * Click on Graph
  * Select a metric from the dropdown
  * Click Execute

And you are done 🙂

<img class="aligncenter size-full wp-image-202" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?fit=640%2C303" alt="Prometheus Metrics" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?w=1920 1920w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?resize=300%2C142 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?resize=768%2C364 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?resize=1024%2C485 1024w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/02/Screenshot-from-2018-02-14-20-24-14.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

&nbsp;

### Where to head up next

By now we have already deployed and configured Prometheus to monitor&#8230; itself!!! But, Prometheus can do much more useful tasks other than monitoring itself. And here comes the concept of Exporters which provide integration with other tools. The exporters are usually written using the Prometheus client libraries and run independently. These exporters expose the data collected by them over some pre-configured endpoints from which Prometheus can then scrape this data.

To take a look at what all exporters already exist, go and head up to this [link](https://prometheus.io/docs/instrumenting/exporters/) where you can find the list of already developed exporters. If there is no exporter which is currently available for your use case, don&#8217;t be shy and develop one. The development guide can be found out at this [link](https://prometheus.io/docs/instrumenting/writing_exporters/).

Happy monitoring!