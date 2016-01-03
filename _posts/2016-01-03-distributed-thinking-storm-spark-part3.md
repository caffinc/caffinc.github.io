---
title: "Distributed Thinking: A gentle introduction to distributed processing using Apache Storm and Apache Spark - Part 3" 
author: admin
layout: post
permalink: /2016/01/distributed-thinking-storm-spark-part3/
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

Welcome to Part 3 of Distributed Thinking! Here I discuss about How to look at data in a distributed environment with a more complex example, Word Count. In case you missed anything, click here for [Part 0](/2015/12/distributed-thinking-storm-spart-part0 "Distributed Thinking").
<h1>1. Distributed Thinking</h1>

<h3>1.3 How to look at data?</h3>
Let's look at the Word Count problem in blocks:

![Word Count](http://caffinc.com/wp-content/uploads/2016/01/WordCount.png)

Here the `Connector` and the `Output` remain the same, with the `Connector` reading out of a file line by line, and the `Output` printing out the result onto the screen.


***
Work in progress, please check back later

----

{% include distthink/navigation.md %}