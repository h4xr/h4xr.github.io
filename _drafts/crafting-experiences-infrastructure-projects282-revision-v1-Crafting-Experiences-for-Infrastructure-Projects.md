---
id: 283
title: Crafting Experiences for Infrastructure Projects
date: 2020-04-25T16:02:14+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2020/04/25/282-revision-v1/
permalink: /2020/04/25/282-revision-v1/
---
Being an engineer, there is always something new which you will learn with everything which you do. Over the period of last year, one of the major things I have learned is about not just building products which are supposed to be handed over to the users, but to craft experiences which the user gets to feel when they interact with your products.

Open in the wild, there are a number of products which offer a great lot of useful features but do not see too much of traction just because the experience they provide to the user is underwhelming, whereas there are other products which are not feature rich, but the experience they provide to the user, makes them the tool of choice.

Being an engineer who has majorly worked on Infrastructure related projects, I have found myself trying to figure out what could be a better way to craft the experience for the projects I work on, such that the interaction which the user feels while working with them is smooth and pleasant. As me and my other team mates worked on improving the projects, there were a great deal of discussions we undertook to figure out what works for our users and what doesn&#8217;t, and every discussion left us with a great deal of ideas which we can implement. This blog post discusses some of the learnings which I had while working on infrastructure tools and projects so that we do not only develop software, but also craft the experiences which are provided by the tools we build.

## Pillars of Infrastructure Tools UX

When building projects which support the engineering infrastructure across your organisation, it becomes really important to understand what might affect the usage of the tools which you are working on. While working on these projects, there were a couple of important things which acted as the pillars of building the user experience:

  * **Usability: **Infrastructure projects usually help the engineering teams layout the core technologies on which every other thing i built and runs. This makes it very important for these projects to be easy to use. The steeper the learning curve becomes to understand even the basic usage, the more hesitation a user will have onboarding and trying to use a project.
  * **Features: **The kind of features a particular tool exposes is another important factor. Can the tool handle the full domain of the problem which it is supposed to solve, or the user will require interacting with multiple tools to solve a single problem. This can sometimes become a make or break kind of decision which can significantly affect the adoption of the tool.
  * **Portability: **Engineers are working on a wide array of problems, the last one which they want is to use a different tool as they switch from one environment to another environment to solve the same kind of problem. Tools which are portable across the different underlying platforms, usually get adopted more widely than the tools which are not portable and require specialised environment for working correctly.
  * **Consistency: **The experiences provided by the tools should be consistent in nature. When a user is using a particular tool, they should not experience an abrupt change in the process they are following because sometimes, this could break the flow of work which the user might be in.

Now with the pillars of the infrastructure tools UX defines, how can we make sure we try to provide our users with the best possible experience when they use our tools?

## Crafting Experiences

Crafting experiences for tools require the engineers working on those tools to not only have an understanding of the infrastructure they are solving the problems for, but also about what the user expectations look like from the tool they are building. For building pleasant experiences which the user enjoys while working with your tools need certain decisions to be made.

### Understanding who your users are

Before any kind of experiment can be done related to the user experience for the infrastructure projects, a very important thing to understand is, who is going to be the user?

Sometimes, when the project is developed, it is built keeping a very narrow point of view in terms of who is the user and as the project usage starts to expand, the users get a feeling that maybe the tool was not built for them. This happens because we didn&#8217;t factor in correctly the expertise of our users.

Sometimes, the user may be an infrastructure expert but not someone who is familiar with application development, other times it could be vice-versa. As developers working on infrastructure projects, we need to make sure both kind of users are served well. _This can be done by making sure, that the projects provide a usable and consistent alternative user experience._ For example, a CLI tool if possible can also have an accompanying UI which the users who are not too familiar with CLIs can use and vice-versa.

### **Balancing between the usability and verbosity**

Infrastructure tools have an inherent tendency to be extremely verbose by exposing every single piece of the underlying infrastructure as a configuration option. Although, this seems to be important to provide the users with the liberty to configure things as they like, this at times can make the learning curve extremely steep for the users. The usability versus verbosity is something the tool engineers have to strike a balance between. _This at times could be done by making sure a basic set of essential options are asked from the user whereas for the other options, intelligent defaults are used._ This allows the new users of the tools to easily onboard them, while also provides the veteran engineers to customize the usage as per their needs.

### **Generalisation vs Specialisation**

Is your tool going to general to the problem domain or a special one focusing on a subset of it? This is an important decision to be made. _Sometimes, the problem domain to be solved is extremely large and it makes sense to have different tools solving the problems of different subdomains._

The reason for this is, when the problem domain is large, the tool will also have a lot of moving components where every component may need its own configuration. In this case, no matter how much we try to make up by using intelligent defaults, our users will still get exposed to un-necessary settings which could have been potentially avoided.

### 

### Bringing Portability into Focus

One of the very important things to focus upon while working on an infrastructure related project is to make sure portability is maintained. Every user works in a different environment which is productive for them, and unless the project is highly specialised, _it is important for the project to be able to run on the different platforms without too much special knowledge from the user side._

Having this portability, greatly improves the adoption for the projects. This happens because the user finds himself at ease of not setting up a specialised environment to run the tool in.

### Abstracting Un-necessary details

As an end user, no one wants to be greeted with a large log of exception stack trace in case an error occurs. This is something which the developers need to take care of. Anything which is not a concern of the end user should potentially be abstracted away from them.

_A good option to achieve this abstraction is to setup proper auto detection of the underlying environment, showing friendly error messages to the user while also making sure any important detail is stored in an easily accessible manner._ For example,

<pre class="line number1 index0 alt2"><code class="text plain">StudentException: Error finding students</code>
<code class="text spaces">        </code><code class="text plain">at StudentManager.findStudents(StudentManager.java:13)</code>
<code class="text spaces">        </code><code class="text plain">at StudentProgram.main(StudentProgram.java:9)
        ...
        ...</code></pre>

Instead of showing something like what is represented above, a potentially good way could be:

<pre>There was an error finding the student record. The error log has been
generated under /home/sbadhwar/student_finder.log.

Please reach out to the development team with the log file attached.</pre>

### Providing Smooth Transitions

When building tools, the transition from one tool to another tool should not be abrupt. The shared components and options should provide the same structure and schemas, whereas things that differ should be easily in focus. This is specially important for tools which are being built to solve subdomain of problems under a large problem domain.

_All the tools being built under the same umbrella should try to provide a similar kind of experience such that the learning curve for the user can be reduced to the maximum possible extent._ This will help them in easy onboarding as well as provide them a familiar working environment.

### Reduce Redundancy

Sometimes, there could be 5 different tools which are solving the same problem set, but differ from each other by very minor implementation details. This at times can become confusing and can also waste a lot of time for the end users who will potentially have to find out which tool is best suitable to their use cases.

_It is always good to add the small and subtle changes into an existing tool rather than rolling out a completely new tool_. People already have a great deal of technical challenges they are working on, making a choice of tool should not become one of them.

These have been some of the learnings which I had while working on tools and projects that span the domain of infrastructure engineering. Hope you find them useful.

* * *

_Are you someone who has worked on infrastructure related projects? Please feel free to comment about what worked out for you which helped you improve the user experience of the projects you engineered 🙂_