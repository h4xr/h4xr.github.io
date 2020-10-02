---
id: 184
title: 'PostgreSQL: Supercharging the query processing'
date: 2017-08-22T17:19:10+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=184
permalink: /?p=184
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";N;s:10:"author_url";N;s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";N;s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";N;}'
categories:
  - Uncategorized
---
We as developers, do come across databases, atleast once in our journey. Hence, we all know, how huge an impact a database can make on the performance of an application. There is an enormous time that is spent in tuning the database queries, such that they perform in an optimal manner and utilize the underlying resources so as to provide the fastest possible response time.

There is no doubt, a well written query beats down any other kind of optimizations hands down, but nonetheless a well optimized database server along with well written queries can do wonders when talking about performance. So, without wasting much of the time, let&#8217;s see, what I am trying to cover up here in this article.

### Databases and their love for memory

Database servers do some really compute intensive tasks while handling user queries about data(well, there is a lot going on behind the scenes for that sweet looking SELECT statement in SQL). These tasks involve a lot of data structure manipulation and hence also generate quite a good amount of intermediate data which is very frequently used by the database engines before they generate the final result for you. All this intermediate data that gets generated, needs some place to live in until it gets discarded.

As most of us from the field of computer science knows, no matter how fast the secondary storage technology has became in the past, we are still not close to the speeds offered by RAM. The amount of delay that exists in data access from memory is far less than the amount of time it will take to access the same data from disk(yes, SSD&#8217;s have improved the situation, but still, we have some considerable difference which may matter in performance intensive tasks).

So, as you might have guesses, the higher the amount of memory, the more amount of data a database system can store in it, which indeed helps in improving the performance a lot.

### Let&#8217;s talk Postgres

So, in case of databases, we are spoiled with the choices. Talk about RDBMS and we have MySQL, Microsoft SQL Server, IBM DB2, etc. Each of these comes with their own pros and cons and at the end it&#8217;s upto the developer to decide, which road they want to take. But with all these choices in my hand, I am going with Postgres? This is one question, which I feel I should answer, because, for the rest of the article, you are gonna see a lot of Postgres.

So, the reason why I am focusing on Postgres in this article is

  * It is open source
  * It has very detailed documentation
  * It offers a lot of power to user for customization when it comes to performance
  * It is supported by a good amount of community (not very huger, but decent)
  * And last but not the least, it is one of the database with which I am dealing for quite some time

So, with the reasoning provided, let&#8217;s jump straight into how we can tune Postgres to make the best available use of system memory and see how it can help us supercharge our query processing.

### Tuning Postgres for Improving Query Processing

&nbsp;