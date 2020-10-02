---
id: 211
title: Building A Dynamic Inventory System for Ansible
date: 2018-05-04T07:36:50+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=211
permalink: /2018/05/04/building-dynamic-inventory-system-ansible/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"5b38d56a7715";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:85:"https://medium.com/@h4xr/building-a-dynamic-inventory-system-for-ansible-5b38d56a7715";}'
categories:
  - Ansible
  - Development
  - Projects
tags:
  - development
  - golang
  - inventory management
  - Open Source
  - programming
  - REST
---
Working with Ansible for most of the time during my day to day Job, dealing with the Ansible inventory files has become a daily task now. These inventory files contain a lot of important information about the hosts that Ansible needs to act upon. This information may contain, host grouping which groups a certain number of hosts into a single hostgroup, host local variables, variables that are applicable to a certain hostgroup, etc.

When you have a fixed number of hosts that needs to be dealt with or a small number of hosts, then managing the Ansible inventories manually does not seem to be too much of an issue because all you have to do is to fire up your your favorite text editor, open the inventory file and make changes to it. But, this process becomes cumbersome, when the number of hosts are large, and adding on that, they are frequently changing. For example, consider having 20K hosts for which are used for different purposes every other day(One of the use case we deal with on a daily basis). Now imagine, you have to fire up a text editor daily, make changes manually to all these hosts (it can be moving a host from one hostgroup to another or changing host local variables). This process is cumbersome as well as highly prone to errors.

Having something which can help manage really large inventories in an easy to use fashion can become something of great help. While trying to figure out what we can do to manage this situation, we ended up creating a simple tool for our CI project which can help with the management of inventories for Ansible. This tool was built as a simple Microservice and hence can be used as an independent solution for managing dynamic inventories also.

Since this project came into inception as an independent microservice for our Bolt-CI project, it has been simplistically named as [bolt-inventory](https://github.com/h4xr/bolt-inventory)

So, let&#8217;s talk about the design goals which we had in mind for the bolt-inventory manager service:

### Design Goals for Bolt-Inventory

Before we started building the inventory manager, we had a certain set of goals we wanted to achieve, some of these goals have been mentioned below in a brief:

  * **Provide an easy to use Inventory manager:** This involves how we interact with the inventory manager to add new hosts to the inventory database and how anisble can easily get the required information from the service.
  * **Prevent loss of data if the inventory manager goes down:** Crashes are inevitable. While we strive to make the Inventory service less and less prone to crashes, we still have to anticipate that the service may crash at any point due to N number of different reasons which we cannot control. The best we can do is, to save the user from data loss that may occur if the service crashes, because, a crashed service can be brought back online again, but the lost data cannot be recovered that easily. So, preventing data loss was one of the important aspects of what we wanted to do.
  * **Provide efficiency in managing large inventories:** People use Ansible for managing small as well as really large set of hosts and we need to take care for both the use cases. Hence, while the inventory manager was being developed, we did focus on how to best manage large number of hosts.

These were a short set of design goals which influenced the way the inventory manager has been developed. So, coming on to what features we built into inventory manager, let&#8217;s see.

### What we have on the table with Bolt-Inventory

Without wasting much of the time, let&#8217;s just directly jump on what features does Bolt-Inventory offer to the user:

  * **RESTful endpoints to manage the inventory:** The inventory manager allows us to manage the inventory through the use of RESTful endpoints that are exposed by the service. These endpoints currently allow us to create hostgroups, hosts and host local facts. These endpoints can also be used to get the complete inventory, hosts grouped under a single hostgroup and a few other things.
  * **Automatic Inventory Save:** To reduce the chances of data loss due to unanticipated crashes, Bolt-Inventory automatically saves the information about the host data present in the service to the disk at a regular interval of time. This time interval known as flushInterval can be configured through the configuration file which is read by the Inventory service at the start of the service daemon.
  * **Provide easy integration with Ansible:** Bolt-Inventory provides a simple tool known as inventory which can be passed as an argument to the ansible command. The ansible command can then automatically get the inventory specific information from the tool.
  * **Easy to run Independently:** One of the project goals was to allow us to run this inventory manager as a part of CI as well as independently. Thus one of the feature of bolt-inventory is that we can run the inventory manager as an independent solution which also allows us to scale it as per our needs. Just deploy it in a container and you can have multiple instances running to cater to your needs 🙂

Since, we now have a basic idea of what we provide as a feature set, lets move on to how to setup bolt-inventory manager.

### How to use Bolt-Inventory Manager

Bolt-Inventory manager is a project which is written in Golang and we release binaries for Linux x86_64 platform release to release. The project is currently only compatible with Linux and can be built from source if the underlying architecture you are running on is different from the ones for which the binaries are provided. All the links to the project have been mentioned at the end of the post.

#### Step 1: Getting the Binaries

The binaries for the service can be obtained from [Github](https://github.com/h4xr/bolt-inventory/releases) where the project is hosted. Once the tar has been downloaded, the binaries can be found under the bin folder.

#### Step 2: Creating the required configuration files

Since we still don&#8217;t have packages available for your favorite Linux distribution, creating the required configuration files is currently a manual step.

The configuration file is a JSON file which looks like the one given below:

<pre>{
  "DataStorePath": "/etc/bolt/db/inventory.db",
  "FlushInterval": 5000
}</pre>

The config file contains two settings:

  * **DataStorePath:** This setting needs a value pointing to the path where bolt-inventory manager can periodically save the inventory database. If the DataStorePath does not exist, inventory manager will try to create it and will fail if the DataStore creation fails.
  * **FlushInterval:** The setting defines the time in milliseconds at which the inventory service should save the in-memory inventory data to the disk

You can create this inventory file at a place whichever you find convenient. If this configuration file is not found, the inventory manager tries to load it from /etc/bolt/inventory.json

#### Step 3: Starting the service

Once we have the configuration file in place, we are good to start the inventory service. Under the bin folder, there are two binaries, the inventoryd binary runs the inventory management daemon. To launch the daemon, enter the following command:

_./inventoryd -configFile &#8220;<path to your configuration file>&#8221;_

For example, on my system, I entered the following command

_./inventoryd -configFile &#8220;/home/sbadhwar/inventory.json&#8221;_

If no error occurs, the service will start running on your host at port 8250

**Step 4: Creating a simple hostgroup and host on inventory manager**

Once the service is up and running, you can use the REST endpoints exposed by the service to manage inventory.

To create a hostgroup using a simple cURL call, the following command can help create a hostgroup:

_curl -X POST -H &#8220;Content-Type: application/json&#8221; -d &#8216;{&#8220;hostgroup&#8221;: &#8220;testgroup2&#8221;}&#8217; http://localhost:8250/create/hostgroup -v_

To create a host under the newly created hostgroup:

_curl -X POST -H &#8220;Content-Type: application/json&#8221; -d &#8216;{&#8220;hostgroup&#8221;: &#8220;testgroup2&#8221;, &#8220;hostname&#8221;: &#8220;m4.example.com&#8221;}&#8217; http://localhost:8250/create/host -v_

#### Step 5: Use your inventory in Ansible

When you take a look into the bin folder, there is one more binary named inventory. This binary is the client for inventoryd service that can be passed to ansible.

To use the inventories from inventory manager, you can do the following example command:

_ansible -i ./inventory -m ping testgroup2_

If you were testing with a host that did not exist, this is how the output will look like

<img class="aligncenter size-full wp-image-213" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/05/Screenshot-from-2018-05-04-12-50-16.png?fit=640%2C75" alt="" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/05/Screenshot-from-2018-05-04-12-50-16.png?w=1306 1306w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/05/Screenshot-from-2018-05-04-12-50-16.png?resize=300%2C35 300w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/05/Screenshot-from-2018-05-04-12-50-16.png?resize=768%2C90 768w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/05/Screenshot-from-2018-05-04-12-50-16.png?resize=1024%2C120 1024w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

&nbsp;

### Contributing to Bolt-Inventory

The bolt-inventory project is completely Open Source and hosted on Github under the MIT License and we are actively looking for contributors to improve upon the functionality of the project. Someone who will like to contribute to the project will require some knowledge of Go and working on Linux. Understanding of how ansible works will be of great help. Some of the good areas where one can start contributing are:

  * Pickup and solve Good first issues
  * Help with the packaging of the tool
  * Help improve the test suite
  * Enable automated build to build testing for the project

&nbsp;

### Resources

Moving further, the following resources might come in handy:

  * Information about Ansible can be found [here](https://www.ansible.com/resources/get-started)
  * Bolt-Inventory project can be found at [Github](https://github.com/h4xr/bolt-inventory/)
  * The list of API endpoints available through the project can be found in the [README](https://github.com/h4xr/bolt-inventory/blob/master/README.md)

&nbsp;