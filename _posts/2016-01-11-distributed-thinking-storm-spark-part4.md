---
title: "Distributed Thinking: A gentle introduction to distributed processing using Apache Storm and Apache Spark - Part 4" 
author: admin
layout: post
permalink: /2016/01/distributed-thinking-storm-spark-part4/
comments: true
categories:
  - Storm
  - Spark
  - Java
  - Scala
tags:
  - storm
  - spark
  - scala
  - java
  - distributed
  - big data
---

<img src="http://spark.apache.org/images/spark-logo-trademark.png" alt="Apache Spark Logo" style="height: 100"/><img src="http://storm.apache.org/images/logo.png" alt="Apache Storm Logo" style="height: 100"/>

Welcome to Part 4 of Distributed Thinking! Here we discuss processing data streams. In case you missed anything, click here for [Part 0](/2015/12/distributed-thinking-storm-spart-part0 "Distributed Thinking").
<h1>2. Processing Data Streams</h1>
I'm glad you survived so far! Now let's get down to processing things using technologies designed to process them, instead of relying on plain old Java Threads.

In the previous post, I discussed how to write a Word Count program using a threadpool in Java. Let's quickly map that program into two versions:

1. Storm
2. Spark

<h3>2.1. Storm</h3>
Let me start off with a brief introduction to Storm. Storm started off as Twitter Storm, a technology to process the massive amounts of streaming Tweets that were pouring into Twitter.

I first used Twitter to actually process batches of data for a TV viewership product. Lots of lessons were learnt. Now it's Apache Storm, and I know what it's best used for. Our word counting problem isn't exactly a "data stream" problem, but we can see how Storm can still help us bring up a distributed system to process the data anyway.

The major components of Storm can be explained with the image below:

![Storm Topology](https://storm.apache.org/documentation/images/topology.png)

You can map this to the current system as follows:

1. The Spout acts as a Data Source (We only need one Spout, though Storm supports multiple Spouts).
2. The Bolts before the last level act as the Mappers/Filters.
3. The Terminal Bolt acts as the Data Sink.

As Storm is designed to process continuous streams of data, it provides ways to retry processing of an object in the stream, but does not have an easy way to stop the system once it's started.

A Storm system is started and made to process data streams continuously without stopping. Let's look at how we'd map our Word Count problem in Storm and solve it:

{% gist c4fd440f561ba52ed7e0 %}

It consists of the following components:

1. LineSpout - This `emits` lines from the text file to be processed by downstream processes.
2. TokenizerBolt - This tokenizes the line into words, converts them to lowercase and `emits` it with a count of 1
3. CounterBolt - This is the Data Sink which stores the data in a static Map. Ideally we'd write off the results into a database from one of the bolts, or use some other strategy of collecting the data.
4. The `main` method - This submits the "Topology" to Storm for processing. 

Here we kill the topology after a few seconds of execution, with the assumption that all the bolts are done processing the data left inside them. However it is important to note that it's difficult to have a deterministic way to stop Storm as it's not designed to be stopped after being started except when someone's absolutely sure that they don't want to process any more data.

I will discuss an elaborate strategy to make Storm a little more deterministic later.

<h3>2.2. Spark</h3>
Spark is a relatively new kid on the block, but has been around for a few years and has proven itself as a solid solution. Hadoop filled a gap in the distributed systems world when there was no good solution, but Spark took it to the next step by bringing very fast computation aided by the use of immutable structures and in-memory processing.

Spark is very easy to map into our system. The Data Source is called an RDD, the mapper is a `map()` method call on the RDD, the filter is a `filter()` method call, and the data sink is a `reduce()` method call. They chain very easily to form a very readable processing system which makes working with distributed systems a breeze.

Let's look at the word count problem from a Spark perspective:

{% gist 7934e56dc93846917e8e %}

Now that is a *lean* solution. Smaller than most other solutions, and perhaps just as flexible. The bit after the `collect` is just to sort the result to be more legible, but the solution is essentially just a couple of method calls in Spark/Scala.

***
Work in progress, please check back later

----

{% include distthink/navigation.md %}