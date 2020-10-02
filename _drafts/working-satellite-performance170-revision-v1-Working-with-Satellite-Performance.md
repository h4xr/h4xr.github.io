---
id: 171
title: Working with Satellite Performance
date: 2017-04-05T10:12:09+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2017/04/05/170-revision-v1/
permalink: /2017/04/05/170-revision-v1/
---
Managing infrastructure in an IT environment is a big deal. There is a lot of time and human effort that goes into building and managing the IT Infrastructure so that it runs efficiently and provides good performance. At Red Hat, we have a lot of clients that use our products across the different parts of their infrastructure. Customers want an efficient and easy way to manage their infrastructure and we at Red Hat are committed to provide that.

#### What is Red Hat Satellite?

Red Hat Satellite is an infrastructure management tool by Red Hat that is specifically designed to keep the Red Hat Enterprise Linux Environment and other Red Hat Infrastructure keep running efficiently, properly secured and managed.

#### What is Satellite-Performance?

We at Performance Engineering group at Red Hat work with different products to find out the optimal settings and configurations through which our products can provide the best performance and scalability. To test and find out optimal settings and configurations for the different products, the performance and scalability engineering team at Red Hat tests the different products on a huge number of machines concurrently. Sometimes, the tasks involve replicating the user environment by creating a number of virtual machines and containers to test the products. This is something which we do with Red Hat Satellite. This work is huge and really tiresome if we have to configure the machines one by one and manually. To save us from this, we have built some set of tools that help us automate our work of setting up the products easy. These tools are responsible for setting up our test environments and labs on which we then perform our operations.

One of the tools which we have built for Red Hat Satellite is Satellite-Performance. The tool helps us prepare our satellite and capsule servers as well as help us provision docker hosts and containers with different settings with ease. This tool can turn out to be handy for other people too, who are dealing with the installation and preparations of Satellite. The tool can be located on the [Redhat-performance GIT repository](https://github.com/redhat-performance/satellite-performance).

#### Satellite-Performance Requirements

Satellite-performance is a tool which relies on [Ansible](https://www.ansible.com/) to do the automation over different hosts. Before moving onto how the tool can be used, we need to setup our execution environment with all the required packages.

For this tool to run, we need to install Ansible. That can be done by running:

pip install -r requirements.txt

With the execution environment setup, now we can move ahead and understand, how we can use satellite-performance to provision and configure our hosts that are going to run Satellite or capsules.

#### Checklist for Running Satellite-Performance

With the execution environment configured to run the tool, we are almost done. Next, we need to make sure, we have some of the things handy, before we configure the Satellite-performance tool as covered in a later section in this blog. Things you will require before going ahead to configure your tool are:

  * Subscription to Red Hat Satellite
  * Red Hat Subscription Manager Credentials
  * Manifest File
  * VMs or Bare Metal Hosts to Configure

If you have all the requirements satisfied, you can go on to the next section which covers the configuration part of the satellite-performance tool.

#### Configuring Satellite-Performance

The satellite-performance tool consists of some files which describe the configuration for the tool. This configuration is used by the different parts of the tool, to setup some values and fields. These configuration files are stored in the conf folder.

**conf/hosts.ini**: This file consists of the name/ip of hosts, that needs to be configured by the tool. The addresses provided under the satellite6 section, will be used to install and configure the satellite on. The addresses mentioned under the capsules section will be used for configuring and installing capsule.

**conf/satperf.yaml**: The satperf.yaml file consists of the different configuration options that are required by the satellite-performance tool to work. If you plan to override the defaults or configure some values you need to copy the satperf.yaml as satperf.local.yaml in the conf directory. This can be achieved by running:

<pre>cp conf/satperf.yaml conf/satperf.local.yaml

<strong>Understanding Different Configuration Options in satperf.local.yaml</strong></pre>

The satperf.local.yaml consists of different options, let&#8217;s see what they mean:

satperf_hosts: The name of the file that stores the address of hosts

satperf\_private\_key: The ssh key that will be used to work with the satellite-performance tool

satperf\_log\_file: The log file that will be used to log the steps

rhsm_user: The username for the Red Hat Subscription Manager

rhsm_pass: The password for the Red Hat Subscription Manager

rhsm_pool: The pool to which RHSM should attach to

sat\_repo\_file: The path to repo file to be used that will be used to install Satellite

sat\_install\_pool: The pool from which the Satellite package can be installed

capsule\_install\_pool: The pool from which the Capsule package can be installed

#### Running satellite-performance

After setting up the configuration for the tool, the next thing to do is, add the required host addresses to hosts.ini. For example,

<pre>[satellite6]

sat1.example.com

sat2.example.com

[capsules]

capsule1.example.com

capsule2.example.com</pre>

After setting up the hosts, the next thing in row is to deploy the ssh keys from the tool host to the machines that needs to be configured. To do this, first we need to generate the SSH key on host. This can be done by running:

<pre>ssh-keygen -t rsa -b 4096</pre>

Accept the defaults and you will have a SSH Keygen at your disposal.

Next thing we need to do is to deploy this key to the other hosts, this can be done by:

<pre>ssh-copy-id root@sat1.example.com</pre>

When you run this command, you will be asked for your password, enter it. This will deploy the key to your remote host. To verify, if the process worked, try executing:

<pre>ssh root@sat1.example.com</pre>

If you are successfully able to login, without the need of entering your password, then the ssh key deployment worked perfectly.

Repeat the above steps, for the other hosts too.

With all this done, we are ready to move forward to installing our satellite and capsules.

**Installing Satellite**

To install satellite, we need to run the following command from the root of satellite-performance folder:

<pre>ansible-playbook -i conf/hosts.ini playbooks/satellite/installation.yaml</pre>

**Installing Capsule**

<pre>ansible-playbook -i conf/hosts.ini playbooks/satellite/capsules.yaml</pre>

**Setting Up the Content**

<pre>ansible-playbook -i conf/hosts.ini playbooks/satellite/clien.yaml</pre>

With all this done, we have our satellite installed and configured 🙂

Find it interesting? Want to help us make it better? Feel free to find issues and fix them.

Resources:

  1. Satellite Performance [Github Repo](https://github.com/redhat-performance/satellite-performance)
  2. What is [Ansible](https://ansible.com)
  3. What is [Red Hat Satellite](https://www.redhat.com/en/technologies/management/satellite)