---
id: 226
title: Working around PostgreSQL Autovacuum Performance Issues
date: 2018-11-01T13:15:09+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=226
permalink: /2018/11/01/working-around-postgresql-autovacuum-performance-issues/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"34b773c50285";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:93:"https://medium.com/@h4xr/working-around-postgresql-autovacuum-performance-issues-34b773c50285";}'
categories:
  - Debugging
  - Optimization
  - Performance
tags:
  - autovacuum
  - cleanup
  - database
  - debugging
  - foreman
  - optimization
  - performance
---
PostgreSQL is one of the well renowned database in the application development areas, where it finds its use in a large number of projects. This can be attributed not only to its open source development model but also to the extent of customizations that it provides to configure the individual components of the database backed by a thorough documentation supporting them. At times in most of the large environments where the workloads are database heavy, we might find ourselves tuning one or the other component of the database.

These tunings mostly revolve around changing the memory that can be allocated by the database processes to hold temporary results, or increasing the number of possible clients that can connect to database, modifying the size of WAL buffers, cost planning for execution of queries and a lot of other things. But beyond this, we expect a certain number of things to behave in an ideal way by themselves without messing around with their settings. The least we expect is the fact that one of the processes that runs to just clean up the dead tuples and to reclaim the disk space could turn out to be a major performance bottleneck for the applications that depend on the database.

This is one of issues which we experienced while debugging one of the performance issues of our application. The article goes on to explain what kind of issue we faced, what were the possible workarounds we considered and how we improved the outcome of the issue.

## The Issue

Being into performance engineering, one of the interesting things is that the issues which come are quite exciting. Most of the time, resolving these issues involves a thorough debugging, moving to and fro inside the application codebase trying to figure out where exactly the issue is happening and all of this when two or more components are interacting with each other.

Just like this, one day we got to know about an issue where one of our users provided us a feedback that our application stops responding in their environment when they run a particular workload. After some debugging they have found that PostgreSQL auto-vacuum is causing lock contention and brings the whole application to a halt(or bring it to crawling speed) in a matter of few hours once the workload is executed.

With this, we atleast had an idea that what is the possible place an issue might be happening. After some investigation, we were able to verify that indeed PostgreSQL AutoVacuum is indeed the culprit. But the real question arises, why was it happening? Essentially it is one of the most harmless process that could actually interfere with the application. Now was the time to find out, why this issue was happening?

## Doing the Root Cause Analysis

Since we already knew which component was responsible for causing the issue, the majority of the effort was now focused upon why the component is behaving erratic and how it is able to bring our application to a halt. The first phase of starting the root cause analysis was to enable the debug logging of PostgreSQL which can provide us much more insight into what the database is doing at any particular point of time. Some of the settings which we modified to achieve this are as listed below:

log\_min\_duration_statement: 500

log\_lock\_waits: on

log_duration: on

log_checkpoints: on

track_activities: on

track\_io\_timing: on

log\_autovacuum\_min_duration: 0

One all of these settings were applied, we restarted the database server and made the workload to run again. As expected, we saw that the processing of workload got stuck in between and this was the time when we wanted to explore what is going on with the database server.

To see which activities were indeed running something on the database server, executing the following query turned out to be a real help:

watch -n1 &#8220;echo \&#8221;select usesysid,usename,state,<wbr />waiting,query from pg\_stat\_activity where state = &#8216;active&#8217;\&#8221; | psql&#8221;

As the time progressed, we saw that there were 3 AutoVacuum processes that kept running for a duration which was higher than 20 minutes and that too on the same set of tables. Now, this was interesting. Why was the Autovacuum process taking so long on a single table. The next thing we started analyzing was the logs of PostgreSQL for that duration to see if there is some lock contention or some other thing that could be possibly causing such a long runtime for the autovacuuming job.

As we dug through the log, we got to know there is nothing related to lock contention, but rather more hints about the fact that on a certain set of tables, Autovacuum is firing quite frequently. Now we needed some kind of theory about why this issue could possibly happen. One of the best guess that can be made is that these particular tables could be experiencing heavy updates hence crossing the autovacuuming thresholds frequently.

Now, it was time to validate what we are thinking is right or wrong. The next thing inline was to look forward to the PostgreSQL statistics collector which could provide us more indepth information about the state of the tables which we wanted to examine. The following query helped us to get the data about the respective tables:

SELECT * FROM pg\_stat\_all_tables WHERE relname = &#8216;<table name to be examined>&#8217;;

From the output of this command, our assumptions got validated. The following set of tables were indeed turning out to be the relations which were being modified quite frequently:

  * public.qrtz_triggers
  * public.sessions
  * public.qrtz\_fired\_triggers

What we saw from our analysis was, these tables were able to cross the autovacuuming thresholds in a matter of a few seconds itself and would accumulate a lot of dead or updated tuples very frequently that an autovacuum run will be required. But such frequent modifications of the these tables was expected due to the way our application behaves.

Now, this has a repercussion, if these 3 tables are running autovacuum so frequently then there will be several performance bottlenecks which we will see:

  * The activities which depend upon these tables will go slow because there was some lock waiting involved
  * If the autovacuum processes are busy on only a small set of tables, then the other tables might accumulate a lot of modified or deal tuples reducing their performance in terms of lookup speeds
  * Increased disk utilization because of a lot of tables having dead tuples

Now, it was the time to find out the possible solutions.

## Narrowing Down the Solutions

Every problem has a solution and sometimes a lot of them and as engineers it comes to us to weigh in the cost of implementing the solution and same was the case here. This problem came with multiple solutions that we had to weigh in for the best balance between the user satisfaction and performance improvements. So, let&#8217;s take a look at what we had in mind:

### Resolve the issue in the application codebase

This is one of the ways which can fix the issue forever. But, this involves a lot of time and rewrite of a considerable amount of logic. With the amount of effort involved, this solution could not only turn out to be slow to deliver to the user who is having troubles in their day to day operations but could also turn out to be less than effective because of the fact that some of the frequent updates to the relations can not be reduced. So, we had to rule out this option.

### Disable Autovacuum

The next thing which we could do (and indeed we tried), was to disable the erratic component of the database itself. Now, this proved to be useful immediately. The workload that was getting affected, is now running perfectly fine and not only that, we saw overall improvements in the performance across the whole application itself(Yayy! moment). But this lasted only till the user reported back that their disk has went out of space.

Ohhh! How did we forget this. This was supposed to happen.

One of the things which the PostgreSQL Autovacuum does and is designed for is to find out the tuples that have been modified or has been marked as deleted. Once it has identified those tuples, the next thing it does is to cleanup the relations containing those tuples resulting into compaction of the table and reclamation of the disk space. But, once we disable the Autovacuum, we essentially disable this whole process which helps in reclaiming the disk space. The worst effect is realized on application which modify a lot of relations resulting in the disk frequently going out of space.

### Increase AutoVacuum Process Count

Another way to resolve this issue is to increase the number of processes autovacuum can launch. This can help in more number of processes which might be available to run the Vacuum and analyze on the tables. But this benefit could easily run out of the effect is there are more tables that are write intensive. Beyond this, one more repercussion is that the higher number of autovacuum processes would also cause increased resource utilization on the system stressing the system even more.

### Increase the Autovacuum thresholds

Now, if we don&#8217;t want to increase the autovacuum processes, what we can do is, we can increase the threshold values at which autovacuum runs. These factors are governed by the following settings inside the postgresql configuration:

  * autovacuum\_vacuum\_scale_<wbr />factor
  * autovacuum\_analyze\_scale_<wbr />factor
  * autovacuum\_vacuum\_threshold
  * autovacuum\_analyze\_threshold

But modifying these factors on a global database level isn&#8217;t going to bring us much relief because the tables which are getting updated at a high pace will still run more autovacuum than the tables which don&#8217;t see updates that frequently.

## The Solution

Now, with most of the options ruled out, we were now wondering what to do? And then it came to the mind, what if we could increase the autovacuum thresholds on a per table basis rather than increasing it globally. This will cause the autovacuum to run a bit less on the heavily modified tables and hence reducing the lock contention as well as providing other tables a chance to go through the autovacuuming process also.

We now started digging around if what we were thinking was possible or not and soon we stumbled upon a few queries which allowed us to modify the autovacuuming properties at a table level basis during the runtime itself. The queries that turned out to be at our help are the following:

<div>
  ALTER TABLE <table_name> SET (autovacuum_vacuum_scale_<wbr />factor=0.3);
</div>

<div>
  ALTER TABLE <table_name> SET (autovacuum_analyze_scale_<wbr />factor=0.4);
</div>

<div>
  ALTER TABLE <table_name> SET (autovacuum_vacuum_threshold=<wbr />1000);
</div>

<div>
  ALTER TABLE <table_name> SET (autovacuum_analyze_threshold=<wbr />1500);
</div>

<div>
</div>

<div>
  With these queries, we now had the power to control the autovacuum to run less frequently over the tables that were seeing very frequent updates. A major benefit of this approach was the fact that these settings were table specific and hence the other tables in the database still trigger the autovacuum as per their original configuration.
</div>

<div>
</div>

<div>
  Along with this, we also marginally increased the number of autovacuum processes that can be launched by PostgreSQL. This was done so as to allow more tables to run autovacuum in parallel.
</div>

## The Results

With every thing done and implemented for the user, we were now waiting to see if our approach made some difference or not? And here are the results:

### Results Before Implementing the AutoVacuuming tunings

**Count           Operation           Relation Name**

791                VACUUM           public.qrtz_triggers  
179                VACUUM ANALYZE public.sessions  
129                VACUUM           public.qrtz\_fired\_triggers  
70                VACUUM ANALYZE public.qrtz\_fired\_triggers  
40                VACUUM ANALYZE public.qrtz_triggers

### Results After Implementing the AutoVacuuming tunings

**Count           Operation           Relation Name**

289               VACUUM           public.qrtz_triggers  
136               VACUUM ANALYZE public.sessions  
129               VACUUM          public.qrtz\_fired\_triggers  
70               VACUUM ANALYZE public.qrtz\_fired\_triggers

And these results provided us with a fair enough amount of reassurance that the tunings indeed were working as expected. Also, as a side-effect of this, the workloads that depended upon these tables also saw some performance gain and became more responsive.

Overall this was a win with a significantly lower cost in comparison to going for the approach involving code changes which would have not only taken a large amount of effort but would have also turned out to be slow to ship to the user.

So, this was how we tuned up the PostgreSQL Autovacuum to provide our application with an increased performance and responsiveness.

Got any kind of questions or suggestions that worked out for you while trying to improve the performance of your database dependent application, write out in the comments 😀