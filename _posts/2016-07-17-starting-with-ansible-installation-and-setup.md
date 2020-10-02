---
id: 50
title: 'Starting with Ansible: Installation and Setup'
date: 2016-07-17T05:22:48+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=50
permalink: /2016/07/17/starting-with-ansible-installation-and-setup/
image: /wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-46-36.png
categories:
  - Ansible
  - Automation
tags:
  - Ansible
  - automation
  - infrastructure
  - IT
  - management
  - Red Hat
---
Managing IT infrastructure is a pain. Imagine having the task to keep the software stack up to date on every node, or monitoring their uptime, Kicking in more servers when the load increases or to deploy a new software, and many other services. Comes Ansible, a tool to configure and manage the IT infrastructure by automating the processes.

### **Installing and Configuring Ansible**

To work with Ansible, we need to setup a controlling machine and nodes. The controlling machine is the one which is used to configure and manage the nodes, this management happens over an SSH connection with the node. The best thing here is the fact that when a node is not being managed by Ansible, no resources are used.

**Control Machine Requirements:** Any machine that supports Python 2.6 or 2.7 can be configured as the controlling machine for Ansible. Supported OS for setting up control machines are Fedora, Cent OS, Red Hat, Debian, Ubuntu, OS X, etc. (Windows isn&#8217;t supported as Control Machine).

**Managed Node Requirements:** Managed nodes must have Python 2.4 or higher, ssh to be installed so that the control machine can communicate with the managed node.

For this post, I will be using Ubuntu 16.04 as the control machine and Fedora 25 (Rawhide) running in a VM as the managed node.

**Installing Ansible on Controlling Machine**

Installing Ansible is a pretty straightforward process. On your Ubuntu based control machine, run the following commands:  
`<br />
sudo apt-add-repository ppa:ansible/ansible -y<br />
sudo apt-get update<br />
sudo apt-get install ansible -y<br />
` 

This will install Ansible on your control machine. The next step is to configure the ssh key for ansible, run the below command to configure the ssh key on the control machine.

`<br />
ssh-keygen -t rsa -b 4096 -c "user@localhost.com"<br />
`  
Now, the ssh key has been created on the control machine, the next task is to copy this ssh key to the managed node. To do this, we need to know the IP address of the managed node. To get the IP address, type in the following command on the managed node:

`sudo ifconfig | grep "inet"`

<img class="wp-image-51 aligncenter" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-32-39.png?resize=562%2C137" alt="Managed node IP address" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-32-39.png?resize=300%2C73 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-32-39.png?w=619 619w" sizes="(max-width: 562px) 100vw, 562px" data-recalc-dims="1" /> 

From this, we will get the IP address of the managed node, which in my case is _192.168.122.139_

The next thing to do is to get the ssh key copied to the managed node, to do so, run the following command on the control machine:

`ssh-copy-id root@192.168.122.139`

**Note**: If in this step you face some error like, connection refused by the remote host, then check whether ssh service is running on the managed node or not. If not, you can start the service by running the following command `sudo service sshd start`.

After copying the ssh key, verify that your control machine can communicate with the managed node over ssh. This can be done by running the following command on the control machine:

`ssh root@192.168.122.139`

If you are successfully able to connect to the managed node, your ssh key has been properly configured on the managed node.

**Configuring Ansible on Controlling Machine**

Now that we have installed Ansible on the controlling machine, the next step is to configure it, so that it can talk to our managed nodes. Configuring Ansible at this point is pretty straightforward.

Type in the following command on your controlling machine:

`sudo nano /etc/ansible/hosts`

and add the following section to the hosts file:

`<br />
[webservers]`

192.168.122.139

And save the file.

What we have done here is that, we have added the IP address of our managed node into the hosts file of Ansible. Ansible uses this host file to establish a ssh connection with the managed node over ssh.

Now, it&#8217;s time to test whether our configuration worked or not.

On your control machine, run the following command:

`ansible -m ping webservers`

If everything is good the output should look something like this:

<img class=" wp-image-52 aligncenter" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-46-36.png?resize=476%2C281" alt="Ansible Ping" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-46-36.png?resize=300%2C177 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/07/Screenshot-from-2016-07-17-10-46-36.png?w=732 732w" sizes="(max-width: 476px) 100vw, 476px" data-recalc-dims="1" /> 

&nbsp;

This marks that Ansible is able to successfully communicate with our managed node over the ssh connection.

In the next part, we will see how can we execute commands on the managed nodes through Ansible and how can we use Playbooks, which are used to enforce our configuration policy on the managed nodes.