---
layout: post
title: "JVM Profiling Disclosures"
date: 2012-11-30 22:46
comments: true
categories: [java,jvm,profiling]
---

Well, I've never actually given importance to the term "*profiling*" till a few days ago. The real deal was that I was just too lazy to actually run a profiler and then to try interpret the stats and hopefully find the bottleneck in the code. So to avoid all this hassle, I tried to be careful while writing code to make sure I don't leave any open doors for memory leaks or poor CPU utilization. 

But a few days back, I stumbled into a performance issue in my code at work, leaving both me and my project lead puzzled about the culprit. Atlast desperation lead us to setup YourKit profiling in our server to have a peak under the hood of the JVM. And in the process, we happened to learn some new terms and functionalities of the profiler and I must say that I'm slowly beginning to like the idea of profiling code. I was really surprised at what we could learn about our code just by looking at the profiler stats. So here below, I've merged in some of our learnings along the whole process.


###JVM Memory Structure###

 It's important to first understand the structure of the JVM memory itself. Simply put:

*JVM Memory = Heap Memory + Non-Heap Memory + Other (JVM code itself, JVM internal structures, loaded profiler agent code and data, etc.)*

###Profiling Terms###

Next, lets look at a few terms you need to know in order to make some sense out of all those weird graphs.

* *Heap Memory* - Stores Java objects
* *Non-Heap memory* -  Stores per-class structures and other meta-data
* *Stack Memory* - Java only stores primitives on the stack. Stack values only exist within the scope in which they were created. Java objects are created on the heap, and only references (which in turn are primitives) are passed around on the stack.
* *Allocated Memory* - All memory that is allocated by the JVM
* *Used Memory* - Amount of memory that is actually in use
* *Eden* - Objects that are newly created reside here
* *S1(Survivor Space 1) or S2* - All objects that survive a GC cycle go here
* *Young Generation* - Eden + S1 + S2 section of Heap memory
* *Old Gen Space* - Objects that survive a fixed number of GC cycles go here

   Ex: Singletons, Cached Objects and other application global data
* *Shallow Size(object)* -  Amount of allocated memory to store the object
* *Retained Size(object)* - Shallow size of object under consideration  + Shallow size of other objects that can be directly or indirectly accessed from this object


###Things to watch out for while profiling###

* In a typical application there will be less variation inside the Old Gen space.
* If the Old Gen space grows linearly with time even after the GC cycle, it would lead to a <code>OutOfMemoryError</code>. This occurs mainly due to JVM memory space depletion (Java Heap, Native Heap, etc.). This might be a indication of a memory leak inside the code. 

The <code>OutOfMemoryError</code> mainly occurs due to the following:

* Lack of understanding of current JVM utilization
* Inappropriate JVM GC policy used. (Monitor JVM GC closely. Enable <code>verbose</code> GC option and analyse the data)
* Lack of performance and load testing  


From the above, you probably would understand most of the important stats when you look into your profiler output next time. Now comes the important question 

> How do I generally find/prevent bottlenecks in my code ?

Well, I've tried to provide a few ideas below:

* Understand your application's memory footprint 
* Provide Connection Timeouts for any sort of communication API that you use.
* Cache data whereever possible to avoid more I/O operations on disk
* Monitor your threads closely to make sure there aren't any bottlenecks or, well Deadlocks (*gulp*)
* Monitor Network Latencies. They are the hidden culprits of many performance problems.
* Implement application logging. This will help you to internally monitor the performance issues and hotspots in your code.
* Use Client JVM for development and Server JVM in production. Most people ignore this change. Even I did so. That was mainly because I never knew the difference between the JVM's. The Server JVM does heavy optimization of your code to increase performance and reduce memory and CPU footprints, in production.
* Increase JVM Heap by specifying <code>-Xms</code> and <code>-Xmx</code> while running your application
* Analyse what's happening under the hood in the heap, using <code>jmap</code> and <code>jhat</code> (Haven't really tried this out)
