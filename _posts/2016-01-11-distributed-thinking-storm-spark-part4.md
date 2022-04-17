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

Welcome to Part 4 of Distributed Thinking! Here we discuss processing data streams. In case you missed anything, click here for [Part 0](/2015/12/distributed-thinking-storm-spart-part0 "Distributed Thinking").
<h1>2. Processing Data Streams</h1>
I'm glad you survived so far! Now let's get down to processing things using technologies designed to process them, instead of relying on plain old Java Threads.

In the previous post, I discussed how to write a Word Count program using a threadpool in Java. Let's quickly map that program into Storm and see how things work.

<h3>2.1. What is Storm?</h3>
<img src="http://storm.apache.org/images/logo.png" alt="Apache Storm Logo" style="height: 100"/>

Let me start off with a brief introduction to Storm. Storm started off as Twitter Storm, a technology to process the massive amounts of streaming Tweets that were pouring into Twitter.

I first used Twitter to actually process batches of data for a TV viewership product. Lots of lessons were learnt. Now it's Apache Storm, and I know what it's best used for. Our word counting problem isn't exactly a "data stream" problem, but we can see how Storm can still help us bring up a distributed system to process the data anyway.

The major components of Storm can be explained with the image below:

![Storm Topology](https://storm.apache.org/releases/current/images/topology-tasks.png)

You can map this to the current system as follows:

1. The Spout acts as a Data Source (We only need one Spout, though Storm supports multiple Spouts).
2. The Bolts before the last level act as the Mappers/Filters.
3. The Terminal Bolt acts as the Data Sink.

As Storm is designed to process continuous streams of data, it provides ways to retry processing of an object in the stream, but does not have an easy way to stop the system once it's started.

A Storm system is started and made to process data streams continuously without stopping.

<h3>2.2. Word Count using Storm</h3>
Let's look at how we'd map our Word Count problem in Storm and solve it:

{% gist c4fd440f561ba52ed7e0 %}

The code consists of the following components:

1. LineSpout - This `emits` lines from the text file to be processed by downstream processes.
2. TokenizerBolt - This tokenizes the line into words, converts them to lowercase and `emits` it with a count of 1
3. CounterBolt - This is the Data Sink which stores the data in a static Map. Ideally we'd write off the results into a database from one of the bolts, or use some other strategy of collecting the data.
4. The `main` method - This submits the "Topology" to Storm for processing. 

Here we kill the topology after a few seconds of execution, with the assumption that all the bolts are done processing the data left inside them. However it is important to note that it's difficult to have a deterministic way to stop Storm as it's not designed to be stopped after being started except when someone's absolutely sure that they don't want to process any more data.

I will discuss an elaborate strategy to make Storm a little more deterministic later.

***
Work in progress, please check back later

----

{% include distthink/navigation.md %}