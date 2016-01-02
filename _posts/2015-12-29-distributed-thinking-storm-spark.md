---
title: "Distributed Thinking: A gentle introduction to distributed processing using Apache Storm and Apache Spark" 
author: admin
layout: post
permalink: /2015/12/distributed-thinking-storm-spark/
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

<h1>0. Introduction</h1>

This post is meant to serve as a starting point for people using Java or Scala to process large amounts of data, and need a quick introduction to how to do it - either in Spark or in Storm.

It is not meant to be a Spark vs Storm debate, there are plenty of those out there. A quick Google search yields several [StackOverflow questions](http://stackoverflow.com/questions/24119897/apache-spark-vs-apache-storm) and technical [blogs](http://www.infoworld.com/article/2854894/application-development/spark-and-storm-for-real-time-computation.html) talking endlessly about it.

This is meant to be a starting point for people new to the whole concept of distributed processing of data, and need a headstart. It's 2015, and my blog post is probably 5 years too late, but it's never too late to get started!

<h3>What I plan to talk about:</h3>

1. Distributed Thinking
    1. When to use it?
    2. Where to start?
    3. How to look at data?
2. Processing Data Streams
3. Processing Large Data Chunks

--------------------------------------------------------------------

<h1>1. Distributed Thinking</h1>

When I first started processing large amounts of data for a project I worked on which involved data from TV viewers (A lot of logs that set-top boxes throw up), I didn't quite understand what "large" amounts of data meant. I had only worked on datasets involving a few thousand records, stretching up to a million at max.

When a colleague of mine wrote a solution to the project using Storm, I didn't quite understand all the moving parts. I was still learning how to code better than the average fresh-out-of-college-computer-science-student, and everything looked complex and alien to me. I have since discovered that if you focus on something long enough, and tear it down into smaller parts, everything is easy! I hope to bring some of my understanding into this blog to make it easier for people new to this kind of data.

<h3>1.1 When to use it?</h3>

Suppose you have a data set that you need to perform some actions upon. Nearly all problems involving data can be broken into an `ETL` process - `Extract`, `Transform` and `Load`:

![ETL](http://caffinc.com/wp-content/uploads/2015/12/ETL.png)

(Image drawn using [Draw.io](https://www.draw.io/ "Draw.io") - Check it out, it's awesome!)

Your data comes from a Data Store, or a Data Warehouse, gets transformed in some way using either Java, Scala or Python job running on one or more machines, and the output gets written into a Data Sink.

When the amount of data coming in from the Data Store is huge, and there's a concentrated focus on making the `Transform` part of the ETL run as smoothly as possible for the "big data", tools such as Storm and Spark help you manage your computation in a distributed environment.

Do you have data that's in the order of several GBs or TBs (or PBs) that has to be processed as soon as possible, maybe even instantly? That's your cue to make your system fit into the ETL model, and write a distributed `Transform` stage.

Often systems like Spark and Storm take time to start up, along with the overhead of actually setting up a distributed cluster. If the advantages that distributed processing brings do not outweigh the disadvantages of the infrastructure and the small processing overhead that exists, you should probably reconsider - I have found that sometimes just a multithreaded implementation in Java was more than enough.

<h3>1.2 Where to start?</h3>
Now that you know when it makes sense to use a distributed system, the question is - Where do you begin?

Let's take a step back and look at our `ETL` system. It is divided into 5 parts - the `Data Store` or `Data Warehouse` (Let's call it `DS`), the `Extract` method (`XT`), the `Transform` process (`TR`), the `Load` method (`LD`) and the `Data Sink` (`SNK`).

    DS ---(XT)---> TR ---(LD)---> SNK

Focus on each of these components now:

<a name="ds"></a>
<h4>1.2.1 Data Store DS</h4>
The DS is the source. There can be several ways in which our system ingests the data. Let's look at the most common ones:

* Relational Databases
* NoSQL Databases
* Data Streams
* Logs and Flatfiles

![Data Store Types](http://caffinc.com/wp-content/uploads/2016/01/DataStoreTypes.png)

These data stores can be broadly split into two types of processing strategies:

* Batch - Databases and flat files
* Real-Time - Streams like Twitter Firehose, Kafka, Message queues, etc.

Traditionally, <b>Spark is more suited for Batch processing, and Storm is suited for  real-time processing</b>, but both can do either type of processing, and what you actually end up using is a matter of preference.


<a name="xt"></a>
<h4>1.2.2 Extract XT</h4>
Depending on the type of raw data your system has to process, there are a few strategies you can use to `Extract` the data:

<a name="1_2_2_1_as_is"></a>

1. Use data as-is:<br>
To do this, your `XT` method would have a connector which connects to the DS and provides a cursor for the data, filters to clean up the data as it arrives from the raw source, and data parsers to convert the data into the structure expected by the `Transform` methods downstream. You would mix and match several parsers and filters here, trying to achieve the fastest, cleanest way to get the data to transform.<br>
Some Data Stores might already provide clean data that can be used as-is, like Twitter or Kafka. If you need to process this in real-time, a Storm solution might make more sense.

2. Transform data into a smaller, or better local data store:<br>
Your data might not arrive at convenient intervals, or it might make sense to only process your data periodically, and not as it arrives. If this is the case, it makes sense to read the raw data source when the data is made available, and store it locally to be processed when it's time.<br>
Your secondary store could be a smaller, and more curated (using methods mentioned in [(1)](#1_2_2_1_as_is) above) as you might only want specific parts of the data. You can also use this process to store the data from a raw format such as Flat files and logs on FTP, into a better format, like a Database, or HDFS. A Spark solution might make more sense to process this data.

Now that the data has been extracted from the raw Data Store `DS`, you can use it in the `Transform` step using either <b>`RDDs` in `Spark`, or `Spouts` in `Storm`</b>.

<a name="tr"></a>
<h4>1.2.3 Transform TR</h4>
Now if you need a real-time solution, `Storm` may be the way forward. If you need a batched processing solution, `Spark` may be the way forward.

Pick whichever one you think would be faster for you to develop, and whichever is easier to maintain.

<a name="ld"></a>
<h4>1.2.4 Load LD</h4>
The choice of your Data Sink `SNK` would affect the way you write your `LD`, but more on that later.

<a name="snk"></a>
<h4>1.2.5 Data Sink SNK</h4>
Your final output gets written to the final form from which it's possible to generate any kind of results you want:

1. Report Generation
2. Page Generation
3. Data Insights

Your output might be to a database, or to files, or to a web service. We'll discuss more about this later.
   
***
Work in progress, please check back later