---
id: 243
title: Profiling Your Node.js Application at Runtime
date: 2019-03-24T16:38:14+00:00
author: saurabh_badhwar
layout: revision
guid: http://saurabhbadhwar.xyz/blog/2019/03/24/239-revision-v1/
permalink: /2019/03/24/239-revision-v1/
---
As developers, we mostly focus on the area of developing applications in the best possible manner. This includes the choice of languages, tools, algorithms, etc. whichever helps us get the best possible outcomes while also keeping the productivity in check. These applications can be the desktop GUI applications, system utilities or Web applications which provides the user the power to achieve their tasks with ease. This development process also involves optimizing the codebase once the slow areas inside an application are identified. But how many time do we take a look at the data about which specific functions or methods are performing slow and are being used frequently? The data related to identifying the hot methods in the codebase could be of great help when it comes to dedicating an effort to optimize our applications.

This post covers the process of how we can profile our Node.js applications during runtime without really interrupting the applications. Also, since the application is not interrupted at all, we can run this in production to identify the performance hotspots while the application serves the live traffic. But, why am I targeting Node.js here and not some other language? The answer to this lies in the fact that, recently I have been involved in trying to find out a way through which we can profile a running Node.js server side application without introducing a restart to the application or generating too much overhead that may cause the application to slowdown causing disruption in the production environment. As this post builds up, we will take a look at why is code level performance profiling required, what techniques are there and then dive deeper into the concepts of how this profiling is made possible in the Node.js world. So, let&#8217;s get going.

# The Need for Performance Profiling The Codebase

Developing an application takes time. Optimizing the codebase is another level altogether. Many a times, developers spend a lot of time optimizing the parts of code that does not translate into any significant performance improvement for the user. So, does this mean the optimizations are incorrect or the application cannot be optimized further? The answer to this is a NO. A lot of applications have a huge potential to be optimized, but this potential is not realized. This happens due to the fact that developers may look at some function and think that it can be optimized, spending a lot of effort into changing the algorithms or the data access patterns inside it, only to realize that the improvement translated into hardly any performance benefit for the user. But why did that happen?

As it turns out, the developer did implement the correct optimizations in the function, but the function was called very infrequently. And this is the case a lot of times. It turns out, when it comes to optimizing an application, implementing a correct optimization is beneficial, but implementing it at the correct place is the one that is going to make an impact. But how do we find out where to optimize? Let&#8217;s find out.

Most of the applications that land into production adhere to some or the other performance metric which acts as a benchmark for them. Most of the time the applications are able to perform well on this benchmark in the average load cases. The real trouble comes, when the application becomes viral and a lot of users start hitting the application concurrently. This is where the performance problems start to arise. As developers or performance engineers, we need to find out where is our application spending most of its time. This is where, the code level performance profiling comes into picture.

The code level performance profiling allows the developers or the performance engineers to look at where their application is spending most of its time. This detail is presented in terms of the name of the functions or methods that are being called, how frequently they have been called and how long they have been running. Not all methods are equal. Some of them may be called less frequently but are exceedingly slow, some of them may be called very frequently but get the work done in a matter of microseconds. It is the responsibility of the developers and the performance team to identify the performance hotspots from the code level profiling. Only if identified correctly and then optimized, the results will create a positive impact. So, how do we go ahead and identify these areas of performance bottlenecks? Let&#8217;s find out.

# Finding Performance Hotspots in the Codebase

When it comes to understanding the performance hotspots in the codebase, the engineers have a lot of handy techniques. Two of these techniques involve, instrumentation of the codebase or doing sampling of it so as to gather the information on where the application spends most of its time. The real part is identifying which technique to use when. So, let&#8217;s take a look at  these techniques and understand what happens in them.

## Codebase Instrumentation

Codebase instrumentation is a well renowned technique which provides a fairly exact estimate of how much time in spent in which part of the application. The details provided in this kind of technique can be really deep which may involve the flow of the request from one function to another, the time spent in each function, the frequency of calling an individual function. This kind of detail can be leveraged heavily to find out which methods may see a greater benefit of optimizations and which can be kept as it is so as to optimize the developer productivity.

In the process of implementing code instrumentation, a developer usually needs to add some specific calls to the start and end of a function so as to estimate how much time a particular function call took. This detail can be enriched by collecting more information about who usually calls a given function through the likes of reflection and other mechanisms as exposed by the different programming languages. This data, enriched with details about the performance related metrics for every single function can be used to correctly and efficiently estimate the performance hotspots in the codebase.

But, as much as the process of instrumentation is useful, it has its own disadvantages. These involve:

  * **Modification of the codebase: **To implement instrumentation, the developer needs to modify the codebase to intercept the calls to individual functions. This kind of changes can have a far ranging effect on the application.
  * **Deoptimized applications: **Another effect of instrumenting the codebase could be the deoptimization of the application. The compilers and interpreters use a large number of techniques to optimize the function calls. There could be cases when the addition of instrumentation code can block these optimizations hence causing the application to slowdown to some extent.
  * **Increased CPU cycles: **Any added instrumentation code is going to consume some of the CPU cycles of your application. Although most of the time it is very less, but depending upon what kind of performance guarantees your application may need to provide, this could turn out to be of significant cost.

It is mostly upon the developers to make a call about how they want to instrument their application. Even if they even want to do instrumentation or not. Most of the time, when the developers resort to instrumentation, they usually only add instrumentation code to only a specific set of functions which they think could be prone to performance issues.

But, we also have another technique, which is less intrusive and more performant in terms of collecting the information related to codebase performance.

## Profiling Through Sampling

With having an understanding of how instrumentation works, there could be times, when a developer may not want to introduce instrumentation code in their application. This kind of decision could have many reasons. So, what can they do? Do they have any other way to get the code level performance metrics? It turns out, indeed there is a way. This process is known as sampling.

With sampling, a developer can invoke a utility which periodically takes a snapshot of the process stack and then estimate which functions were running at that time. This snapshot can involve the snapshot of an individual thread or the whole application at once. A lot of this also depends on what kind of environment the application is running in and if that environment makes it possible to snapshot the application or not.

Since, the snapshots of the application are taken from outside and at a well defined time interval, the overhead introduced on the application is limited. Also, since the process does not involve any kind of modification to the codebase, the application runs unmodified, usually with all the compiler optimizations in place to speed up the run of the application. But as with everything, this process also has its own drawbacks, which include:

  * **Dependent on the application runtime: **The sampling is highly dependent upon the features exposed by the application runtime. If the runtime does not expose the application symbols or does not provide a mechanism for asynchronously capturing the snapshot of the executing application stack(i.e. without bringing the application to a temporary pause), the technique won&#8217;t work or may involve a lot of overhead.
  * **Reduced granularity: **Since the application snapshots are captured outside the application and at some interval, there is no possible way to correctly estimate the time spent in individual methods. This translates into reduced granularity of the collected data.

If the engineers are okay with the trade-offs in terms of metric granularity and require a solution which they can implement external to the application, the sampling based profiling could prove to be of great use. The disadvantage of not having exact data about how much time is spent in the individual function calls can be worked out by estimating the performance through the use of calling frequency and stack traces.

With the basic idea of the two techniques, let&#8217;s now dive into how to do performance profiling for Node.js applications at runtime.

# Performance Profiling Node.js Applications

In the recent years, fueled by Node.js Javascript has started to rule the server side applications space. This Javascript stack has been a massive hit inside the developer ecosystem largely due to a number of ready to use third party libraries providing pre-built solutions to a large number of problems, the common language stack for developing the application at the client side as well as the server side and the power of handling a good number of concurrent users.

With a number of renowned large scale companies such as Uber, Netflix, LinkedIn and others adopting Node.js the ecosystem is gaining a lot of traction and a lot of developers are now moving into the ecosystem building a variety of applications catering to the needs of the different users. This heavy use of the Node.js also brings in a lot of opportunities for optimizing Javascript based applications on the server side.

The Node.js runtime is powered by the Google V8 Javascript engine which is technically a virtual machine executing the javascript code to generate an output. Inside the Node.js world, the optimizing of the codebase would target how much time a request takes to generate a response and send it back to the client, how much time individual functions take to execute inside the V8 Javascript engine.

Luckily, V8 provides us with a lot of power to run performance profiling on our codebase. This performance profiling is made possible through the use of the V8 inspector protocol that allows the developers to debug and sample the codebase that is executing inside the V8 engine.

## V8 Inspector Protocol

The V8 javascript engine provides a lot of useful debugging capabilities. These capabilities can be used both by the users as well as the developers who embed the V8 engine into their application. The protocol provides a method for the users to connect to the V8 engine and inspect its state. The [V8 Inspector protocol](https://v8.dev/docs/inspector) is usually exposed through a websocket and an application is identified uniquely through a UUID. When a user wants to connect to the V8 inspector protocol, they usually need both, the address on which the protocol is exposed and the UUID of the application.

For interacting with the V8 inspector protocol, the developers usually have two interfaces, one is the Chrome DevTools interface and another one is the programming interface. For this post, we will focus on the latter one.

Now, let&#8217;s take a look at how we can use this protocol to run performance profiling on our Node.js application.

## Exposing The V8 Inspector Protocol in Node.js Application

Usually, when a Node.js application is launched, the application does not start with the V8 inspector protocol enabled by default. To enable the V8 inspector protocol, the user needs to provide the _&#8211;inspect _flag to the application.

`$ nodejs --inspect <script_name>`

When the Node.js runtime is launched in this manner, it usually starts a websocket available at ws://127.0.0.1:9229/<UUID> exposing the V8 inspector protocol on the interface to which we can connect and then do our operations with.

But this way of launching the V8 inspector protocol is a bit troublesome in production because for starting the debugger, you first need to bring the application down and then start it again with the flag. This is not possible in every environment and may make the application temporarily unavailable for the users. There ought to be some better way to achieve this:

### Enabling the Protocol at Runtime

As it turns out, we have a way to enable the protocol even when the application is executing. This is made possible through the use of two different mechanisms: Signals on Linux and Mac and through an undocumented internal call for Windows based hosts.

The Node.js runtime is configured to respond to the _SIGUSR1_ signal by starting the V8 inspector protocol. Once any Node.js application is signaled with the SIGUSR1, the application starts the V8 inspector protocol. To do this, the following command can be used:

`$ kill -usr1 <PID of the node.js application>`

For the Windows based hosts, the same functionality can be achieved through the use of an undocumented method call _process._debugProcess(PID)_. For a running node.js application this can be done as:

`$ nodejs -e "process._debugProcess(PID)"`

Once either of the options are used, the protocol can be found running at _ws://127.0.0.1:9229/<UUID>_

## Connecting to the Protocol

Now, once we have the protocol enabled, let&#8217;s use some quick Python scripting to connect to the protocol. For successfully executing the commands that follow, we will need to get a small python library installed on our system. To install this library, run the following command (possibly inside a virtual environment):

`$ pip install websocket_client`

The above command installs a small library which helps us connect with the websockets and interact with them.

Now, once the library is installed, the next thing is to identify where the websocket with the V8 inspector protocol is running. If you have already signaled the application with SIGUSR1, the log message for the application should contain the URL. Otherwise, you may visit http://127.0.0.1:9229/json. The UUID of the application is the &#8220;id&#8221; field that can be found in the JSON for the application name specified.

`$ curl http://127.0.0.1:9229/json<br />
[ {<br />
"description": "node.js instance",<br />
"devtoolsFrontendUrl": "chrome-devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9229/c3453b38-82be-46c3-9d56-39d8783fbb47",<br />
"faviconUrl": "https://nodejs.org/static/favicon.ico",<br />
<strong>"id": "c3453b38-82be-46c3-9d56-39d8783fbb47",</strong><br />
"title": "nodejs[276]",<br />
"type": "node",<br />
"url": "file://",<br />
"webSocketDebuggerUrl": "ws://127.0.0.1:9229/c3453b38-82be-46c3-9d56-39d8783fbb47"<br />
} ]`

Now, with the UUID in our knowledge, it&#8217;s time to open a Python interpreter by running

`$ python`

Once inside the interpreter, import the create\_connection method from the websocket\_client which will help us to connect to the websocket exposing the V8 inspector protocol.

`>>> from websocket import create_connection`

Once the method is imported, let&#8217;s connect to our websocket, by executing the following command:

`>>> ws = create_connection("ws://127.0.0.1:9229/c3453b38-82be-46c3-9d56-39d8783fbb47")`

With this, if everything is running fine, we will be connected to the V8 inspector protocol. The communication over the protocol happens through the exchange of JSON formatted messages. So, we also need to import another library inside our interpreter providing the JSON support. This can be done through the use of the following command:

`>>> import json`

Now, we are all set to run our profiling session on the executing Node.js application.

## Starting the Profiler

With the socket connection established and the JSON support in place, let&#8217;s get going with the profiling.

The first step to start the profiling is to enable the profiler inside the runtime. To do this, we craft the following request JSON:

`>>> enable_profiler = {"id": 1, "method": "Profiler.enable"}`

In the previous statement, we created a Python dictionary, with two keys:

  * **id: **A unique id to track the request. Once a request is taken by the V8 inspector protocol, the protocol returns a response with the same id field as that of the request. This is handy in case of commands where asynchronous execution might be a need.
  * **method: **The method to be executed. The value of this field contains two parts. The Domain and the method to be called. The Domain is like a namespace inside which a particular method lives. For example, in our case, the domain is Profiler and the method is enable.

Once this request is crafted, we can send this request by executing the following command:

`>>> ws.send(json.dumps(enable_profiler, separators=(',',':')))`

Since the websocket based inspector protocol takes in a the request without any whitespace delimeters, we remove any whitespace separators inside our request by setting the separators to use inside _json.dumps()_ method call. Once the command is sent, the response can be checked by executing the following command:

`>>> ws.recv()`

If the request ended with some errors, we will get a JSON back with the error key and error code set.

Now, with this, our profiler is enabled. But right now, it is not doing any profiling on the application.To start the profiling, we need to send another request. The following commands does this for us:

`>>> start_profiler = {"id": 2, "method": "Profiler.start"}`

>>> ws.send(json.dumps(start_profiler, separators=(&#8216;,&#8217;,&#8217;:&#8217;)))

>>> ws.recv()

With the execution of the above commands and if no error was received, our profiling session has already started. Now, let us keep the profiler running for some time. This profiler, implemented by the V8 engine is a sampling profiler which samples the application stack at a specific time interval. By default, the profiler is set to sample every 1000 microseconds.

Now, once we have kept the profiler running for some time, it is time to stop the profiler and get the results. The execution of the following commands stops the profiler and gets us the result.

`>>> stop_profiler = {"id": 3, "method": "Profiler.stop"}`

>>> ws.send(json.dumps(stop_profiler, separators=(&#8216;,&#8217;,&#8217;:&#8217;)))

>>> ws.recv()

The result of the execution of the above command is a JSON containing the following keys:

  * **startTime**: The time at which the profiling started
  * **endTIme**: The time at which the profiling ended
  * **timeDeltas**: An array consisting of the delta between the successive samples. The delta for the first sample is the difference between the startTime and the time of the first sample.
  * **nodes**: An array of Profiler nodes containing the information about the individual method, its line number, source file, etc.
  * **samples**: An array consisting of the node id which was found to be executing when the snapshot was taken

This completes our profiling session with the data with us. It is now upon us how we make use of this data. A lot of times, you might want to generate [flamegraphs](http://www.brendangregg.com/flamegraphs.html) out of the collected data.

Now, it&#8217;s time for us to disable the profiler, which can be done by executing the following commands:

`>>> disable_profiler = {"id": 4, "method": "Profiler.disable"}`

>>> ws.send(json.dumps(disable_profiler, separators=(&#8216;,&#8217;,&#8217;:&#8217;)))

>>> ws.recv()

Once this is done, we can now disconnect from the protocol by executing the following command:

`>>> ws.close()`

and then close the interpreter.

With this, we got to know how we can run a profiling session on a running Node.js application without interrupting the application or introducing severe performance penalty on it, making the technique useful for profiling applications running inside production. Once this profiling data is with us, we can utilize it for finding out the areas in our code which may show a large benefit through the code optimizations.

# Further Reading

In case you found this post interesting and looking for ideas on what all you can achieve through the use of the V8 inspector protocol, head over to the following links and learn more

  * [Debugging over the V8 Inspector Protocol for Embedders](https://v8.dev/docs/inspector)
  * [The V8 Inspector Protocol](https://chromedevtools.github.io/debugger-protocol-viewer/tot/)
  * [Flamegraphs by Brendan Gregg](http://www.brendangregg.com/flamegraphs.html)
  * [Javascript CPU Profiling in Chrome](https://developers.google.com/web/updates/2016/12/devtools-javascript-cpu-profile-migration)