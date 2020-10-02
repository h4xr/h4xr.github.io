---
id: 85
title: Moving forward upon Open Health Data
date: 2016-08-04T06:59:44+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=85
permalink: /2016/08/04/moving-forward-upon-open-health-data/
categories:
  - Projects
tags:
  - data
  - Health
  - healthcare
  - Open Health System
  - patients
---
Recently, I published a post on how I view the concept of the <a href="http://saurabhbadhwar.xyz/blog/2016/07/25/the-concept-of-open-health-data/" target="_blank">Open Health Data</a>. The concept has many benefits, from providing a single place for accessing all your health related data, helping researchers analyze new patterns and to help the doctors take a better decision. There is a lot more that can be done with such a system. But, this was just a concept which I described in mere words, without any proper implementation. But, from the day I published the concept, I was working on something. Things were being done slowly and steadily. Moving on to today, I was successfully able to achieve the first milestone while building the proof of concept system. The current system is able to register and authenticate users and has got the feature to build a basic profile which can be used site wide. The current profile setup is different for the hospitals, doctors and patients. Progress is being made to get this system fully functional by adding some more specific fields for each profile and implementing the permission system.

So, what I am using to build this and what is the technology stack, let me elaborate:

The current proof of concept system is being built using python. Since the main aspect of the current system is providing a web service to manage all the data, Flask is being used as the framework of choice because of the fact that it provides a good amount of decoupling and is lightweight in nature. MySQL currently serves as the database backend for the service.

The aim of this proof of concept is to show how the system will function and will also allow to ponder more closely on the shortcomings of the system as well as to identify the bugs in the early stages of development. This also allows to keep a check on the features and to decide what to implement or if something can be merged.

So, what are the goals for this proof of concept system? Here is a small list of ideas I have:

1). Ability to create profiles based on roles &#8211; Doctor, Hospitals, Patients, Researchers

2). Implement a strong permission system so as to allow only authorized access to the patient data

3). Implement an API which provides the 3rd party applications to access the patient data after the patient has given his approval for the same

4). Ability to easily navigate the patients profile and finding specific data without going through the whole profile.

This is surely a small list of what needs to be done for the proof of concept system but will be able to display the most important functionality of the project.

With this post, I will also like to announce the availability of the initial project source in a closed form because of the relative early stage of the project. If you feel the same about the concept of having an open system for managing the patient health data and want to contribute, the link to the project is attached at the end of this post. Feel free to drop me a mail at contact [at] saurabhbadhwar [dot[] xyz if you will like to evaluate the system in its current stage or to contribute in one way or another.

[1] <a href="https://bitbucket.org/saurabhbadhwar/open-health-system" target="_blank">Bitbucket: Open Health System by Saurabh Badhwar</a>