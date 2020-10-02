---
id: 190
title: Deploying Red Hat Satellite 6 at Scale
date: 2017-08-16T10:27:06+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2017/08/16/186-revision-v1/
permalink: /2017/08/16/186-revision-v1/
---
Red Hat Satellite 6 is a system management product that enables the customers to deploy, configure and maintain there systems across a host of physical, virtual and cloud environments. Under the hood, Satellite 6 uses a number of different components(foreman, qpid, pulp, candlepin, etc.) to provide a host of features such as Remote Execution, provisioning, monitoring, etc. to the customers. These number of components not only make Satellite 6 a very powerful product but also brings along the ability to manage a very huge number of systems.

&nbsp;<figure id="attachment_187" style="width: 1024px" class="wp-caption aligncenter">

<img class="size-full wp-image-187" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/08/satarch.png?fit=640%2C675" alt="Satellite 6 Architecture" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/08/satarch.png?w=1024 1024w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/08/satarch.png?resize=284%2C300 284w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/08/satarch.png?resize=768%2C810 768w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/08/satarch.png?resize=971%2C1024 971w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> <figcaption class="wp-caption-text">Satellite 6 Architecture</figcaption></figure> 

&nbsp;

At Red Hat Performance and Scale Engineering Team, we always strive to make Satellite scale to higher and higher number of systems while keeping the resource usage in check. This scaling of the Satellite requires us to understand and optimize the various components that constitute the Satellite 6, so that they can handle the load of a highly demanding environment.

So, the question comes is, how to achieve these large scale runs of Satellite? Let me provide some brief about how it all works 😉

### The Scale Infrastructure

The Scale Runs of Satellite constitute a beefy hardware which enables us to run a large scale replication of our customer environments enabled by docker powered containers and virtual machines.

The current setup for a medium scale replication consists of the following hardware:

**Satellite Host: **24 Cores/48 GB RAM/1 TB Hard Drive/10G NIC

**Capsule Host:**

<p style="padding-left: 30px;">
  <strong>Capsule VM1: </strong>12 Cores/24 GB/500 GB/10G Bridge
</p>

<p style="padding-left: 30px;">
  <strong>Capsule VM2: </strong>12 Cores/24 GB/500 GB/10G Bridge
</p>

<p style="padding-left: 30px;">
  <strong>9 x Docker Hosts: </strong>24 Cores/48 GB/1 TB Hard Drive/10G NIC
</p>

<p style="padding-left: 30px;">
  Each docker host runs 500 containers, all utilizing overlayfs and network bridged through the host 10G interface
</p>

**Graphite & Grafana Host: **4 cores/8 GB RAM/300 GB Hard Drive/10G NIC

This setting provides us with a single host running Satellite 6, two capsule VMs running capsules and registered to Satellite plus a total of 4500 content hosts built using RHEL 7.3 Docker Images.

### Deployment Automation

As can be imagined from the Infrastructure section, the number of nodes that needs to be configured is quite big, added to that, we also need to deploy and execute commands in 4500 containers that are running. To automate our task of configuring all these nodes and deploying Satellite and Capsules, we have created a some tools using Ansible, which helps us deploy Satellite Infrastructure at Scale[1].

So, how to use satellite-performance to deploy Satellite 6 at Scale?

Previously, I made an attempt to document the use of satellite-performance which can be referred from here[2]

### Deployment Tuning for Scale

For running Satellite 6 on scale, we need to optimize the configuration for some of the components. Let me try to make an effort in explaining what kind of optimizations we make for running the Satellite on scale.

  * **Passenger:** In Satellite 6.x, foreman and puppet, both being ruby applications, depends upon Passenger to route the requests to them. The default value for MaxPoolSize can be optimized to allow more requests to be catered by the components. So, as an optimization, we increase the MaxPoolSize in Passenger to a value that is equal to 1.5x No. of cores in the system.
  * **Foreman/Puppet: **In this case, we increase the number of MinInstances based upon the type of workload the customer has. Also added to this is the fact of Preloading the applications so as to reduce the initial request-response time.
  * **httpd:** httpd acts as a proxy server to route requests to Passenger and Pulp. By default, we have httpd which uses MPM-Prefork, which is configured to serve a limited number of requests only. For running at scale, we increase the number of httpd server instances that can be spawned so as to handle higher concurrent loads.
  * **Pulp:** Pulp acts as a content provider, whenever some content host requests for a repo or application from the yum. To optimize for Scale, we increase the max-pulp-workers to an optimistic value so as to allow faster repo syncs.
  * **QPID:** QPID is the message broker that is used in Satellite to help different components coordinate with each other. As an scale optimization, we increase the number of max\_open\_files for qpid to allow higher number of concurrent connections.
  * **PostgreSQL:** PostgreSQL is used by both foreman and candlepin as a database and is frequently used during the content host registrations and Remote Execution (ReX) queries. The default max\_connections and work\_mem in Postgres is set to a very low value restricting the amount of Content Host related tasks such as Registrations and ReX to be low. In the tunings, we increase these values to support higher number of concurrent executions that can take place.** **

For having a better understanding of what we tune and how to make satellite run on scale, feel free to refer to the carefully curated Satellite 6 Performance and Tuning Guide available here [3].

Also, to make the task of deploying the tuning easy, we have created a small tool, satellite-tune [4] which can be used to deploy the tunings to your Satellite infrastructure.

### Want to monitor your satellite infrastructure? We have a solution ready 😀

Most of the time, when you deploy your Satellite in a scale environment, you will like to monitor your infrastructure for ascertaining that the Satellite is utilizing the resources efficiently, and also to understand, which components are actually becoming a bottleneck in your setup.

To ease out on this monitoring, we have developed a monitoring tool which combines the system statics collection utilities such as collectd, statsd in combination with Graphite to store the system data and Grafana for easy visualization.

To deploy your own monitoring setup, feel free to use our satellite-monitoring tool [5] which will help you deploy all the required components without any hassle.

[1] https://github.com/redhat-performance/satellite-performance

[2] http://saurabhbadhwar.xyz/blog/2017/04/05/working-satellite-performance/

[3] https://access.redhat.com/sites/default/files/attachments/ma-red-hat-satelllite-62-performance-brief-f5544-201703-en.pdf

[4] https://github.com/redhat-performance/satellite-tune

[5] https://github.com/redhat-performance/satellite-monitoring

###