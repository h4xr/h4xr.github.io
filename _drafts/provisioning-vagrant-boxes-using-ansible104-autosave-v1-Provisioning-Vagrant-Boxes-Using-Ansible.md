---
id: 111
title: Provisioning Vagrant Boxes Using Ansible
date: 2016-08-23T06:03:03+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2016/08/18/104-autosave-v1/
permalink: /2016/08/23/104-autosave-v1/
---
Ansible serves as a great tool for those system administrators who are trying to automate the task of system administration. From automating the task of configuration management to provisioning and managing containers for application deployments, Ansible makes it easy. The lightweight module based architecture of Ansible, makes it a good option as a tool for system administration. The advantage lies in the fact that when the node is not being managed by Ansible, no resources are used.

In this article, we will see how we can use Ansible to provision Vagrant Boxes. So, what exactly is a Vagrant Box? In simple terms, we can think of a vagrant box as a virtual machine prepackaged with the development tools we require to run our development environment. We can use these boxes to distribute the development environment which the other team members can use to work on the projects. Using Ansible, we can automate the task of provisioning the Vagrant boxes with our development packages. So, let&#8217;s see how we can do this.

For this tutorial, I am using Fedora 24 as my host system and Ubuntu 14.04 (Trusty) as my Vagrant Box.

#### Setting up the Pre-requisites

For configuring Vagrant Boxes using Ansible, we will require a few things to be setup beforehand. The tutorial requires Ansible and Vagrant to be installed on the host machine. So, let&#8217;s install Ansible first.

On your host machine, execute the following command to install Ansible.

`sudo dnf install ansible`

This will automatically Ansible and the required dependencies on your host system. To test if Ansible has been properly installed, execute the following command:

`ansible --help`

If you get a screen which looks somewhat similar to the one below, Ansible installed just fine for you.

<img class="aligncenter size-full wp-image-105" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Ansible-install.png?fit=640%2C431" alt="Ansible install" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Ansible-install.png?w=734 734w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/Ansible-install.png?resize=300%2C202 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Now, since we are done installing Ansible, let&#8217;s install Vagrant.

To install Vagrant, execute the following command on your host system,

`sudo dnf install vagrant VirtualBox`

The above command will install Vagrant on your host system. As you have noticed, alongwith Vagrant, we are also installing VirtualBox. The need for installing VirtualBox is because, Vagrant doesn&#8217;t provide functionality to host your virtual machines but rather depends on 3rd party providers such as VirtualBox, VMWare, etc. to actually host the VMs.

Now, since we are done with setting up the per-requisites, lets move on and create our first Vagrant Box which we will soon configure using Ansible.

#### Setting Up The Vagrant Box

Before we use Ansible to provision our Box, we need to create one. So, let&#8217;s start by creating a new directory which will store our files related to the Vagrant Box. To create this directory and make it as our current working directory, issue the following command:

`mkdir -p ~/lampbox && cd ~/lampbox`

Now, we have created our directory. Let&#8217;s move on to initializing our Box.

Before we move on to create our Box, let me explain what we will be aiming for. Our box will be a simple one which will run Ubuntu 14.04 as its base system over which we will be running Apache 2, MySQL and PHP5.

A Vagrant box can be initialized using the _vagrant init_ command. For our tutorial, we will be initializing our box with Ubuntu 14.04 as Base. Issue the following command to initialize the box:

`vagrant init ubuntu/trusty64`

This will initialize the Vagrant box and create a file named _Vagrantfile_ with some pre-configured variables.

Let&#8217;s open this file and modify it according to our needs.

`<em>config.vm.box = "ubuntu/trusty64"</em>`

The following line lists the base box which our configuration uses. Now, let&#8217;s setup port forwarding for so that after everything is setup and running, we can test our server. To setup port forwarding, add the following line just before the _end_ statement in Vagrantfile:

`<em>config.vm.network "forwarded_port", guest: 80, host: 8080</em>`

This will map the port 80 of our Vagrant Box to port 8080 off our host machine.

The next step is to set Ansible as our provisioning provider for the Vagrant Box. Add the following lines before the _end_ statement in your Vagrantfile to set Ansible as the provisioning provider:

`config.vm.provision :ansible do |ansible|<br />
ansible.playbook = "lamp.yml"<br />
end`

Here, let&#8217;s focus on the statement  `ansible.playbook = "lamp.yml"`. This statement defines the name of the playbook that should be used to provision our Box.

#### Creating our Ansible Playbook

In Ansible, Playbooks serve the purpose of describing a policy which you want to enforce on your remote nodes. They can be used to manage configurations and deployments on remote nodes. On a technical side of things, a playbook is a YAML file in which we write our tasks which we need to perform on our remote nodes. For our tutorial, we will be creating a playbook named _lamp.yml_ to provision our Box.

Let&#8217;s move forward and create our playbook. To do so, create a file named _lamp.yml_ in the same directory where your Vagrantfile is located and add the following lines to it:

`<br />
---<br />
- hosts: all<br />
sudo: true<br />
tasks:<br />
- name: Update cache<br />
apt: update_cache=yes<br />
- name: Install Apache<br />
apt: name=apache2 state=latest<br />
- name: Install Mysql<br />
apt: name=mysql-server state=latest<br />
- name: Install PHP5<br />
apt: name=php5 state=latest<br />
` 

Now, let&#8217;s iterate over the above file and see what the individual lines mean.

The line _hosts: all_ specifies that our playbook should run over all the hosts defined in our Ansible configuration. Since, we have not configured any hosts yet, the playbook will run on localhost.

Next we have is _sudo: true_ which states that the tasks should be performed with root privileges.

The next line _tasks: &#8230;_ specifies the tasks which should be performed when our playbook is run. Under the _tasks_ section, we have _&#8211; name: &#8230;_ which is used to provide a descriptive name to our task.

The _apt: &#8230;_ line tells that the task should be executed by using the apt module. The statements name, state and update_cache are _key=value_ pairs we are providing to the apt module.

So, let me explain what or playbook does here. When this playbook is executed, it will first update the apt cache and then install the latest version of Apache2, PHP5 and Mysql on our box.

Now, we are done with writing the playbook for our Box. Let&#8217;s move on and provision our Vagrant box.

#### Provisioning our Box

Now, a few final steps remain before we will be using our Vagrant Box provisioned using Ansible.

To run this provisioning, we move forward and execute the following command:

`vagrant up --provider virtualbox`

The above command will start our Vagrant Box and download the base box image on the host system if it is not already present and then run our playbook _lamp.yml_ to run the provisioning setup.

If everything works fine, you will see an output which look somewhat similar to the one shown below

<img class="aligncenter size-full wp-image-108" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?fit=640%2C348" alt="vagrant up" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?w=1366 1366w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?resize=300%2C163 300w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?resize=768%2C417 768w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?resize=1024%2C556 1024w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/vagrant-up.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

The output above shows that our Box has been provisioned. Now, we can go ahead and check if our server is accessible or not. To check this, open your web browser on the host machine and type the following address: _http://localhost:8080_ (remember, we mapped port 8080 of our host to port 80 of our vagrant box). If everything worked good, you will be greeted with the Apache welcome page like the one shown below:

<img class="aligncenter size-full wp-image-109" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?fit=640%2C347" alt="apache welcome" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?w=1366 1366w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?resize=300%2C163 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?resize=768%2C416 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?resize=1024%2C555 1024w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/apache-welcome.png?w=1280 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

And with this, we saw how we can use Ansible to provision our Vagrant Boxes. Although, this was a very basic example, but there are a lot of possibilities like deploying our complete applications along with the up-to-date version of required tools, etc.

Go ahead, try come up with something creative on how you can use Ansible to provision your remote nodes or containers.

&nbsp;