---
id: 118
title: 'Building Microservices: A To-Do Application'
date: 2016-09-05T07:41:32+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=118
permalink: /2016/09/05/building-microservices-application/
categories:
  - Uncategorized
tags:
  - Flask
  - Microservices
  - python
  - Todo
---
Recently, I covered an introduction to what Microservices are and what is the future of Microservices. Keeping that article in mind, I thought, why not make a demo application that can give an example of how Microservices are built and how they interact. In this article, I will be building a small application using the Microservice architecture (MSA). The application will be a super simple To-Do management list. So, let&#8217;s have take a look at what we are going to build and how we are going to build.

#### To-Do Manager: A super simple Microservices Example

So, talking about our application, it is leveraging the MSA where the whole application is divided into a set of services that specialize in doing a specific task using a simple set of protocols. All the communication between different services occur over the network.

Now, for building our Application, we will be making use of Python. Our application uses Flask as the framework for getting the basic things up. Currently, the application uses a few files in the JSON format which serves as the database for our application. For the time being, the application to most part is static in nature.

So, let&#8217;s talk about the architecture of the application:

Currently, our application consists of two services namely the User service and the To-Do service:

  * User Service: The user service provides a RESTful endpoint to list the users in our application and also allows to query the user lists based on their usernames. This service currently runs on port 5000 of our server.
  * To-Do Service: The ToDo service provides a RESTful endpoint to list all the lists as well as providing the list of projects filtered on the basis of usernames. This service runs on port 5001 of our server.

The application can be located at <a href="https://github.com/h4xr/todo" target="_blank">ToDo Manager</a>, feel free to fork and modify it and extend.

So, let&#8217;s setup our development environment and get up with the application.

#### Setting up the development environment

For development of our application, we will be using Python alongwith the Flask. As a personal preference, I use virtual environments for building different python applications since it removes the risk of messing up with the libraries globally on my development system. For this tutorial, I will be using Fedora 24 Workstation. So, let&#8217;s setup our environment by getting the required tools and setting our virtual environment. Run the following command to get the virtual environment.

`sudo dnf install python-virtualenv`

The next step is to create our project directory

`mkdir todo && cd todo<br />
`  
Now, let&#8217;s setup our virtual environment and install the required dependencies for our application:

`virtualenv venv<br />
`  
The above command will create a virtual environment named venv under the application directory.

Next, we need to install the dependencies for our application. Our application is current dependent on two libraries &#8211; Flask and requests. Here, Flask as I have introduced is a web framework and requests is a library which allows us to make HTTP requests.

Before installing the dependencies, we need to activate our virtual environment. So, let&#8217;s do it.

`source venv/bin/activate<br />
`  
The above command activates our virtual environment and now we need to install the dependencies. Run the below commands to get the dependencies installed in our virtual environment.

`pip install flask requests<br />
`  
So, now we are done with setting up our development environment. The next step is to setup our directory structure for the application. Currently, our application has two directories namely database and services. The database directory hosts the files containing some dummy data for the users and todo lists made by the users.

The services directory holds the code for our individual services &#8211; in this case the user service and todo service.

So, before we start coding our services, let&#8217;s get the database setup.

Create a file named _users.json_ under the database directory and add the following to the file:

<pre>{
    "saurabh_badhwar": {
        "id":1,
        "name":"Saurabh Badhwar",
        "verified":1
    },
    "aniket": {
        "id":2,
        "name":"Aniket Sharma",
        "verified":1
    },
    "luckas": {
        "id":4,
        "name":"Luckas Friendel",
        "verified":0
    }
}
</pre>

The next thing we have to do is create another file named _todo.json _which contains the data of our lists. Create the file and add the following data to it:

<pre>{
    "saurabh_badhwar": {
        "home": [
            "Buy milk",
            "Look for pest control service",
            "Get a new carpet"
        ],
        "work": [
            "Complete the blogpost",
            "Create presentation for meeting"
        ]
    },
    "aniket": {
        "school": [
            "Complete homework",
            "Prepare for test"
        ]
    }
}
</pre>

So, now we are done with the database part for our application. Next, we have to build our services. So, let&#8217;s start with writing our User service.

Under the services directory, create a file named users.py and write the code for it:

So, let&#8217;s start with the code, first we import the dependencies for the service

<pre>from flask import Flask, jsonify, make_response
import requests
import os
import simplejson as json
</pre>

The next we do is to initialize our flask service

_app = Flask(\_\_name\_\_)_

Now, we import our users database in the service and parse it as a JSON file

<pre>with open("{}/database/users.json".format(database_path), "r") as f:
    usr = json.load(f)
</pre>

The next step is to write our application endpoints, a simple hello world entrypoint can be created as:

<pre>@app.route("/", methods=['GET'])
def hello():
    ''' Greet the user '''

    return "Hey! The service is up, how about doing something useful"</pre>

The @app.route helps set the application route. The above example helps us setup a hello world application point. When a user visits our application at http://localhost:5000 the user will be greeted with the message we have specified.

Using the same technique we can come up with the other end points for our service. Writing the complete code doesn&#8217;t seem feasible for this post. You can refer to the repository link provided above for the complete code of the application.

The final step in writing the service will be to run the server as soon as the application is called. This can be achieved by the following set of code for our user microservice

<pre>if __name__ == '__main__':
    app.run(port=5000, debug=True)
</pre>

For the todo service you can lookup the repository.  
Go ahead, lookup the repository and build your application.  
Do share, how your experience was in the comments 🙂