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

#Introduction
This post is meant to serve as a starting point for people using Java or Scala to process large amounts of data, and need a quick introduction to how to do it - either in Spark or in Storm.

It is not meant to be a Spark vs Storm debate, there are plenty of those out there. A quick Google search yeilds several StackOverflow questions and technical blogs talking endlessly about it.

This is meant to be a starting point for people new to the whole concept of distributed processing of data, and need a headstart. It's 2015, and my blog post is probably 5 years too late, but it's never too late to get started!

###What I plan to talk about:
* Distributed Thinking
    * When to use it?
    * Where to start?
    * How to look at data?
* Processing Data Streams
* Processing Large Data Chunks

#Distributed Thinking
When I first started processing large amounts of data for a project I worked on which involved data from TV viewers (A lot of logs that set-top boxes throw up), I didn't quite understand what "large" amounts of data meant. I had only worked on datasets involving a few thousand records, stretching up to a million at max.

When a colleague of mine wrote a solution to the project using Storm, I didn't quite understand all the moving parts. I was still learning how to code better than the average fresh-out-of-college-computer-science-student, and everything looked complex and alien to me. I have since discovered that if you focus on something long enough, and tear it down into smaller parts, everything is easy! I hope to bring some of my understanding into this blog to make it easier for people new to this kind of data.

##When to use it?
Suppose you have a data set that you need to perform some actions upon. Nearly all problems involving data can be broken into an `ETL` process - `Extract`, `Transform` and `Load`:

->![ETL](http://caffinc.com/wp-content/uploads/2015/12/ETL.png)<-
->The ETL process<-

(Image drawn using [Draw.io](https://www.draw.io/ "Draw.io") - Check it out, it's awesome!)


----
Work in progress, please check back later