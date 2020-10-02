---
id: 72
title: Up and Running with Docker
date: 2016-07-22T02:51:51+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=72
permalink: /2016/07/22/up-and-running-with-docker/
image: /wp-content/uploads/2016/08/large_h-dark.png
categories:
  - Docker
tags:
  - containers
  - docker
  - docker hubs
  - environments
  - hubs
  - linux
---
In the previous post I talked about Vagrant, a tool which can be used to configure and distribute your development environments. In this post, I will be taking a look on Docker, which can be used for a similar purpose. The aim of docker project is to provide containers, which you can configure for your work. Each docker container runs as a separate process from each other but share only the kernel.

The docker project takes advantage of the linux cgroups and namespaces to provide resource isolation. This type of resource isolation helps different containers run on the same system in distinct processes. So, lets just get started with Docker, install it on the system and get running.

#### Installing Docker

The docker project can be installed without much hassle, the official documentation is very detailed about how to install docker on your system and covers a range of operating systems and architectures. For this post, I will be installing docker on Ubuntu 16.04 (Xenial Xerus).

There are a few steps we need to accomplish before we install docker. Lets go by them one by one.

The first step involves to update our software sources. We can get this done by running the following command

`sudo apt-get update`

The next thing we need to make sure is that apt is properly able to communicate over HTTPS. Running the following command installs the necessary packages (if they are not installed) for apt to communicate successfully over HTTPS.

`sudo apt-get install apt-transport-https ca-certificates`

The next step involves adding the GPG key:

`sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D`

With these steps done, lets move to adding the software sources through which apt can install docker

Run the following command

`sudo nano /etc/apt/sources.list.d/docker.list`

and add the following line in the editor

`deb https://apt.dockerproject.org/repo ubuntu-xenial main`

Save and close this file and update the apt cache by running

`sudo apt-get update`

Now, we have the repository setup, its time to get the docker installed. Run the following command to install docker.

`sudo apt-get install docker-engine`

After the completion of this step, the docker engine is installed on our system.

To start the docker service, run the following command:

`sudo service docker start`

#### Running a simple docker container

After the previous steps, we have docker installed and running on our system. So, let&#8217;s go ahead and test docker by running the basic hello-world container. To run the hello-world container, type in the following command

`sudo docker run hello-world`

After the completion of command you will get a output which looks something similar to the one below:

<img class="aligncenter size-full wp-image-73" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-hello.png?fit=640%2C356" alt="docker hello" srcset="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-hello.png?w=734 734w, https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-hello.png?resize=300%2C167 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Here, we have it, our first docker container running. What docker did behind the scenes was the task that, first it looked for the hello-world container image on your system, if it finds the image there, it picks that image from there and runs it, but, in case the container image is not located on your system, then docker looks that image online in its library and if a match is found, downloads it from there and runs it.

#### Running an Interactive session on Docker

The previous example we ran just started a container and displayed the output and exited. Well, that wasn&#8217;t interactive at all. We can use docker to run in an interactive mode by passing -i as parameter. So, lets run an interactive docker session using fedora container.

Execute the following command to get the fedora container, up and running in interactive mode

`sudo docker run -it fedora bash`

After the successful completion of the command, the screen will look something like the one shown below:

<img class="aligncenter size-full wp-image-74" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-fedora-1.png?fit=640%2C360" alt="docker fedora 1" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-fedora-1.png?w=729 729w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/docker-fedora-1.png?resize=300%2C169 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

And here we have it, docker running in the interactive mode. But, wait, what was that -t option above i passed to the command. Well, the -t option asks the container to a tty console to the container so that we can interact with it.

Let&#8217;s run a few command here and see what happens. For this post, I have ran the _ls_ and _dnf_ command inside the container. The output is shown below:

<img class="aligncenter size-full wp-image-75" src="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/fedora-docker.png?fit=640%2C357" alt="fedora docker" srcset="https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/fedora-docker.png?w=733 733w, https://i0.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2016/08/fedora-docker.png?resize=300%2C167 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

This was a small example of what docker is and what it can do. Currently the docker project is being used in vast array of services, be it running hosting services which provide your app to run online, or services that help you test your code integrations. The project can be used to automate build services which can run inside a container and continue there task. As a user it&#8217;s upto you, how and to which extent you can take the use of docker. It just might be that, you can come up with a new creative idea on how you can use the docker project.

#### Docker Hub

Till now, we have discussed about what Docker is, how we can run containers in it and how to run an interactive session. Well, keeping in mind the capabilities of docker, it can be used to run your servers and other projects. The docker hub is one way through which you can find the pre-built containers of your choice, so, all you have to do is, find the container that best suites your needs and run it with the _docker run_ command. Currently, the docker hub contains a lot of images supporting a vast number of projects.

For further reference you can visit upon some the following links:

1). <a href="https://www.docker.com/" target="_blank">Docker project</a>

2). <a href="https://docs.docker.com/engine/installation/" target="_blank">Docker Installation</a>

3). <a href="https://hub.docker.com/" target="_blank">Docker Hubs</a>