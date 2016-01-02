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

Let's look at a slightly more complex example - Word count. 
***
Work in progress, please check back later


----

{% include distthink/navigation.md %}