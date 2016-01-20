---
title: "Distributed Thinking: A gentle introduction to distributed processing using Apache Storm and Apache Spark - Part 5" 
author: admin
layout: post
permalink: /2016/01/distributed-thinking-storm-spark-part5/
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

Welcome to Part 5 of Distributed Thinking! Here we discuss processing large chunks of data. In case you missed anything, click here for [Part 0](/2015/12/distributed-thinking-storm-spart-part0 "Distributed Thinking").
<h1>3. Processing Large Data Chunks</h1>
In the previous post, we looked at how data's processed using Apache Storm. We also saw how we create Spouts and Bolts to aid us in processing streams of data.

In this section I discuss how to process large chunks of data using Spark.

<h3>3.1. What is Spark?</h3>
<img src="http://spark.apache.org/images/spark-logo-trademark.png" alt="Apache Spark Logo" style="height: 100"/>

Spark is a relatively new kid on the block, but has been around for a few years and has proven itself as a solid solution. Hadoop filled a gap in the distributed systems world when there was no good solution, but Spark took it to the next step by bringing very fast computation aided by the use of immutable structures and in-memory processing.

Spark is very easy to map into our system. The Data Source is called an RDD, the mapper is a `map()` or `flatMap()` method call on the RDD, the filter is a `filter()` method call, and the data sink is a `reduce()`, `count()`, `reduceByKey()` or other appropriate method calls. They chain very easily to form a very readable processing system which makes working with distributed systems a breeze.

<h3>3.2. Word Count using Spark</h3>
Let's look at the word count problem from a Spark perspective:

{% gist 7934e56dc93846917e8e %}

Now that is a *lean* solution. Smaller than most other solutions, and perhaps just as flexible. The bit after the `collect` is just to sort the result to be more legible, but the solution is essentially just a couple of method calls in Spark/Scala.

***
Work in progress, please check back later

----

{% include distthink/navigation.md %}