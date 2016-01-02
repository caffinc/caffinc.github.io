---
title: "Distributed Thinking: A gentle introduction to distributed processing using Apache Storm and Apache Spark - Part 2" 
author: admin
layout: post
permalink: /2016/01/distributed-thinking-storm-spark-part2/
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

Welcome to Part 2 of Distributed Thinking! Here I discuss about How to look at data in a distributed environment. In case you missed anything, click here for [Part 0](/2015/12/distributed-thinking-storm-spart-part0 "Distributed Thinking").
<h1>1. Distributed Thinking</h1>

<h3>1.3 How to look at data?</h3>
As discussed before, our primary goal is to transform the data into the form that we need. Let us take a classic example of counting lines in a text file, starting with a simple version using Java:

{% gist c44b1019d85791ac01c3 %}

This is a simple transformation where the Data Source `DS` is the file in `args[0]`, the connector is the `try` block with a `BufferedReader` and the Extract `XT` method is the `br.readLine()` method.<br>
We filter the data by checking if the line read is `null` or not. The `Transform` process `TR` merely counts all lines that aren't filtered out. It `Transforms` a line into the number `1` and adds it to the count.<br>
The Load method `LD` is the `System.out.println` method which prints the `lineCount` onto the screen, which acts as the Data Sink `SNK` here.

Let's break this program into blocks:

![Line Count](http://caffinc.com/wp-content/uploads/2016/01/LineCount.png)

The `Connector` block is responsible for getting the data from the underlying system.<br>
The `Distributed Transformation Code` block takes the list of lines Line 0 to Line (N-1) and transforms it into a number `1` and then sums it up to `N`, which is the number of lines in the file.<br>
The `Output` block is responsible for delivering the result to the output system, which may be a Data Sink `DS` or the screen.

Let's look at a slightly more complex example in blocks - Word count:

![Word Count](http://caffinc.com/wp-content/uploads/2016/01/WordCount.png)

Here the `Connector` and the `Output` remain the same, with the `Connector` reading out of a file line by line, and the `Output` printing out the result onto the screen.

 

***
Work in progress, please check back later

----

{% include distthink/navigation.md %}