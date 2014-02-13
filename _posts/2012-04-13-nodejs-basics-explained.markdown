---
layout: post
title: Node.js Basics Explained
date: 2012-04-13 10:02:42 -04:00
comments: true
tags: javascript nodejs
---
Rising from non-existence three short years ago, Node.js is already attracting the accolades and disdain enjoyed and endured by the Ruby and Rails community just a short time ago. It overtook Rails as the most popular Github repository last November (now superseded by Twitter's Bootstrap project) and was selected by InfoWorld for the Technology of the Year Award in 2012.

If you've never used Node.js before or have limited experience, it may not be obvious why people are so excited about it. This post will attempt to explain the basic theory central to Node's approach, arming you with a better frame of reference for the debates you'll undoubtedly encounter.

Please note that this post won't make much sense without a very basic familiarity with Node. Consider checking out the [Node.js website](http://nodejs.org) and reading a few articles on Node first if that's the case.

I/O Latency
===========
The core premise behind Node's approach is that I/O operations are slow compared to the computation done in your application. Consider the table below, taken from Ryan Dahl's 2008.11.08 presentation on Node.js. It shows various I/O operations on the left and the number of CPU cycles it takes to perform them. Don't focus on the actual numbers, but on the differences in magnitude for the  operations.

<table>
	<tr><th>Operation</th><th>CPU cycles</th></tr>
	<tr><td>L1</td><td align="right">3</td></tr>
	<tr><td>L2</td><td align="right">14</td></tr>
	<tr><td>RAM</td><td align="right">250</td></tr>
	<tr><td>Disk</td><td align="right">41,000,000</td></tr>
	<tr><td>Network</td><td align="right">240,000,000</td></tr>
</table>
	
From the table you can see that disk and network access times dwarf things like memory access or L1 and L2 cache access. The chart below makes the magnitude differences even more obvious. The L1, L2, and RAM access times are so much smaller than disk and network access that their bars don't even appear on the graph.

![i/o magnitudes](/assets/latency-bar-graph.png "I/O Latency")

Waiting
=======
If you can buy that I/O operations are often orders of magnitude slower than the computation you're performing in your app, then what is your app doing during the I/O operations? It's waiting! It's execution is literally blocked until the I/O operation completes.

Consider the following fictitious web request. The slim green bars represent the time your application devotes to processing and the gray bars represent the time spent waiting for I/O to complete. The example starts off with some logic to process the request. 

1. Parse the request and invoke the appropriate controller logic
2. Initiate a database query or maybe a request to an external service
3. Wait for I/O
4. Process the query results and write some data to a log file
5. Wait for I/O
6. Perform some final formatting of the results and return them to the client

![waiting](/assets/waiting.png "Waiting")

The vast majority of time spent during the request involved waiting for the I/O operations, and there were only two. Very little time is actually used to perform application-specific processing. Node.js was designed from the start to exploit this imbalance.

Scaling
=======
Before we get into the approach Node.js takes to scaling, let's consider how our made-up web request would be scaled with other models.

Scaling with Threads
--------------------
Using a thread based model, you'd scale the above example by creating multiple threads, one for each concurrent connection. The diagram below depicts a threaded model capable of handling four concurrent connections.

![scaling with threads](/assets/scaling-threads.png "Scaling with Threads")

While this approach allows us to scale by adding more threads, each thread still spends most of its time waiting for I/O, not processing your application logic. Unfortunately, continuing to add threads introduces context switching overhead and uses considerable memory to maintain execution stacks.

Scaling with Processes
----------------------
Another popular approach to scaling your application is to run multiple processes. As you can see from the diagram below, the theory behind scaling with multiple processes is basically the same as scaling with threads, although it does use more memory. Like the threading model, each process still spends most of its time waiting on I/O.

![scaling with processes](/assets/scaling-processes.png "Scaling with Processes")

Scaling with Node.js
--------------------
Since the code you write for Node.js executes in a single thread within a single process, it takes a different approach to scaling. It extracts the "I/O waiting" by using an internal thread pool or leveraging asynchronous I/O APIs of the host operating system to free your thread for processing other connections. Instead of your code blocking on I/O operations, it's freed to process other connections. When the I/O operation completes, your code is called back to handle the results.

![scaling with event loop](/assets/scaling-event-loop.png "Scaling with Node.js")

With Node.js your code never blocks for I/O operations, eliminating those long gray bars of waiting time. This non-blocking mode of operation is what allows Node.js to handle large numbers of concurrent connections without overly-straining system resources.

Conclusion
==========
The success of Node.js relies on the premise that time spent waiting for I/O far outweighs the time spent executing application logic. While that's true of many of todays' web/network-based applications, it doesn't always apply. If your application is more CPU-intensive with minimal I/O by comparison, then Node.js is probably not the right platform. As my father used to say, don't try to fit a square peg in a round hole. 
