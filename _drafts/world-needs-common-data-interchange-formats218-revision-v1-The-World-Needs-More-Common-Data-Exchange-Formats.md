---
id: 220
title: The World Needs More Common Data Exchange Formats
date: 2018-07-21T18:14:15+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2018/07/21/218-revision-v1/
permalink: /2018/07/21/218-revision-v1/
---
We live in a world full of choices, where one is free to choose a particular piece of technology they want, which they like, the one which gets there work done. People generate a lot of data using the tools they love &#8211; A lot of articles on wordpress powered blogs, videos encoded with mp4, logical data stored in databases using SQL and other technologies, etc. But what happens when someone plans to move to a new tool? What happens to the data they have already generated? Are we locked down in an open world? These are a few questions which I want to target with this article.

# The Lockdown

<img class="aligncenter wp-image-219 size-large" src="https://i1.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920-1024x681.jpg?fit=640%2C426" alt="The Lockdown" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?resize=1024%2C681 1024w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?resize=300%2C199 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?resize=768%2C510 768w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?resize=480%2C320 480w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?w=1280 1280w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2018/07/padlock-428549_1920.jpg?w=1920 1920w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Though we live in a world which is supposed to be open, where the flow of information should be open from one place to another. That surely doesn&#8217;t seems like a case. For sure one will like the people to use the tools they have built with their hardwork, but not providing the people an option to move the data to a different tool when one will like or making the process really hard that one nearly gives up the energy to move their own data is basically telling the users, you are locked down to my ecosystem.

This is just not done, on one side, you want to call yourself user friendly and on the other side you don&#8217;t want the user to have any easy option to migrate there data to any other place. There are a lot of places where this is true, be it a database or a blogging application. The end result of this is a place where the user has a choice to either stick to a particular application for a lifetime just to retain the data they have already generated or loose all their data and migrate to a new application, starting everything from scratch.

There is something funny here, is this lockdown intentional or is it something which just happens. From what I think, this is partially intentional and partially due to the virtue of how things are developed. Large corporations and for profits may just want to lockdown their users to generate a revenue stream while others who even support the ideas of openness might be forced not to build such a data exchange system due to the sheer number of data formats they might have to support for exporting the data.

So, what can be done? Will we always remain stuck between running a single application for our lifetime because we cannot migrate our data from it?

# Common Data Exchange Formats

There is a solution to our problems, the one which can be adopted if someone wants to, without really caring about supporting the thousands of options that a particular entity might have to care about while implementing the support for exporting the data from their application and another application importing that data. The solution is, common data exchange format.

What is this common data exchange format? How will it help in the process of migrating the data from one place to another? Will it have any benefit? Let&#8217;s try to answer these questions

## What is a common data exchange format?

A common data exchange format serves as a format for storing and exchanging the information of a certain type. Let&#8217;s try to understand this with a simple example of Blogging software.

Let&#8217;s say, we define a standard format which has a field for supporting the blog title, blog abstract, blog description, any media that is embedded in the blog. Now, when a particular blog post is exported, the blogging platform converts its representation of the blog post into this common data exchange format by exporting the information as described by the common data exchange format while also creating a archive of the media files along with this format. Now we end-up with an archive which contains all the textual content arranged in a particular manner inside a file, and all the media files contained in the archive.

Now, the application which needs to import this data, just needs to consult the metadata of the common data exchange format and then import the content from the file, and the contained media files into a format which it supports.

## The advantages

There are quite a few advantages to the approach of common data exchange format, let&#8217;s briefly take a look at them:

  * **No need to worry about all the different data storage formats**: Once the application supports the common data exchange format, it needs not worry about all the different formats that other applications support. All it needs to take care of is about, how the application itself stores the data and how it will translate it into common data exchange format. The application that needs to import the data will only need to care about how to read the common data exchange format and how it will represent the imported data internally.
  * **The freedom of having application specific data format: **One develops a format for storing the data in a particular way because they think that their format will provide a certain advantage which their application can exploit to perform better and this is completely fine. This choice should reside in the hands of an application developer in what way they want to represent the information. The common data exchange format allows for this, it allows the application developer to keep the format they want and also allows them to just add support for one other data representation format which will remain common to all the applications that implement that format.
  * **No lockdowns: **Once a certain number of applications start supporting a common data exchange format, they not only become more user friendly by allowing the user to export the data whenever they want, they also allow the user to have an easy way importing the data from other applications.

# Personal Opinions

I think, having a common data exchange format will be a boon for everyone. Imagine not being forced to use a particular application, having the freedom of choosing what fits your purpose and being able to easily export that data in future when some better application emerges that fits the needs. This no lockdown approach is a way to go, the recent [Data Transfer Project](https://datatransferproject.dev/) found as a collaboration between Facebook, Google, Twitter, Microsoft is one of the examples of this kind of approach and it will be great if other applications also start following the same approach.

Though the approach has a number of challenges like the development of a common standard for a particular type of application, the application developers supporting the common format and the users asking for this type of functionality.