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
By now you must have a fair idea about how to look at and treat your data.

Let's look at the Word Count problem in blocks:

![Word Count](/assets/images/2016/01/WordCount.png)

Here the `Connector` and the `Output` remain the same, with the `Connector` reading out of a file line by line, and the `Output` printing out the result onto the screen.

We have two `Transform` methods:

1. `Transform 1` converts the data from a line into component token words `word_1` to `word_n`
2. `Transform 2` groups words into their respective counters

This is a perfect example for a system implemented in `Map-Reduce`:

1. Map the data into small immutable chunks, in this case Lines 0 to (N-1)
2. Map the data further into smaller immutable tokens, in this case word_1 to word_n
3. Reduce the tokens into one in-memory table of word counts, which is the result

Let's look at a simple single threaded Java implementation:

{% gist 7c590629b55140d855c1 %}

Here I've labeled the transformations as `mapXYZ` methods for mapping, `filterXYZ` for filtering, and `reduce` for reduction into the result. This format of code is very close to being ready for a distributed system. The Mapping and Filtering methods do not have any side effects, and the data being passed to the methods are treated as immutable blocks.

Once all the steps are free of side effects, and the data is treated as immutable, we're ready to distribute the processing on different machines. But before that, let's look at the current block-diagram version of the Word Count program:

![Word Count Single Thread](/assets/images/2016/01/WordCountSingleThread.png)

Now to convert this to a multi-threaded application, the `map` portion needs to be executed in parallel. Let's look at that block diagram:

![Word Count Multi Thread](/assets/images/2016/01/WordCountThreads.png)

The map method is executed in multiple threads. We need a Queue that facilitates the delivery of the data to the mutliple threads with thread safety. The reduce method is out of these n threads to indicate that it runs on one machine and all data is collected into one final step. In reality, this might not be the case, and the reduction might happen in multiple threads and the data finally returned to the main thread. Let's look at the code:

{% gist be8f6dfbbf999cd87c18 %}

Here we have a `main` method which reads from the FileIterator and dumps all the data into the `dataQueue`. It then stars an `ExecutorService` with a few threads which perform Transformations by reading from the `dataQueue` and write the final result into a `counter`.

As the method progresses, all the data is read from the FileIterator and dumped into the queue, and each thread processes whatever data is left on the queue and writes the data into the counter, whose method `reduce` is now made `synchronized`. There are much better ways to perform multi-threaded reduction, which we will discuss later when we look at implementing a system using Spark and Storm.

We now have all the building blocks to create a distributed system which can process your data concurrently on multiple threads. We will now look at solutions for the Word Count problem using Spark and Storm, and see how it helps us solve some of the problems that a single-machine multi-threaded solution poses.

[Click here to go to the next post which discusses Processing Data Streams.](/2016/01/distributed-thinking-storm-spark-part4 "Part 4")

----

{% include distthink/navigation.md %}