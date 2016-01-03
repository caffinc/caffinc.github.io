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

Let's rewrite this program to look like the blocks:

{% gist 25b59892eb88b8609970 %}

Here you can see that the `FileConnector` is responsible for getting the data from the underlying file and providing it for the `Transformation` code. The `Transformation` code is now in two methods. The first one is called `transform1` which converts the String `input`, which is the line, into the Integer `1`. The second one is called `transform2` which adds the count to the `dataSink` which is an `AtomicInteger` in our case, but can be a Database or any other system.

<h5>Immutability and Side Effects</h5>

Here I need to bring up a very important concept called `Immutability`. While there are several ways to explain it, this is the basic concept:

> An Object `x` is said to be Immutable if there is no function F which can change its value.<br>
> i.e. there is no `F(x) = y` where the value of `x` changes after calling `F()`. In other words, you can apply F<sub>1</sub>(x), F<sub>2</sub>(x) ... F<sub>n</sub>(x) but the value of `x` does not change.

Let's look at an example of an object that is immutable and an object that is mutable:

    int x = 10
    def f1(y) = {
        y = y + 20
        print(y) // Prints 30
    }
    print(x) // Prints 10

    AtomicInteger i = 10
    def f2(y) = {
        y.incrementAndGet(20)
        print(y) // Prints 30
    }
    print(i) // Prints 30

Here you can see that the integer `x` is immutable. There is no method that can be called on `x` that will change the value `10`. However, the AtomicInteger `i` is mutable, and the method `f2` changes its value each time it's called.<br>

This brings us to another important concept: <b>Avoiding Side Effects in Functions.</b>

A side effect of a function F is the changes that the function causes to the state of the system outside the function. Let us look at the following method:

    AtomicInteger countA = 0 
    def containsA(x) {
        if (x.contains("A"))
            countA.incr()
    }

Here the increment method `containsA` modifies the value `countA`, which is a side effect of the method. The called of the method does not have any control over the modifications that the method made. Why is this a problem? Look at the following code:

    def runSomething() {
        for (String x : xList) {
            int retries = 3
            do {
                try {
                    containsA(x)
                    doSomething()
                    break
                } catch (e) {
                    retries--
                }
            } while (retries > 0)
        }
    }

Here the method `runSomething()` loops through a list of Strings `xList` and counts the number of As, then does something with `doSomething()`. Now in order to ensure that every line in `xList` is definitely accounted for, `runSomething()` performs retries on failures. This retry-on-failure is a common feature of distributed systems in order to guarantee reliability in large clusters where there can be many causes of failures - nodes are down, network is down, data is corrupt, etc.<br>
In this case if `doSomething()` fails, the String `x` is retried. Now if `containsA` has found A in the String, it counts twice, thus leading to inaccuracy.<br>
Let's rewrite this code to avoid Side Effects:

    AtomicInteger countA = 0 
    def containsA(x): Int {
        if (x.contains("A"))
            return 1
        return 0
    }

    def runSomething() {
        for (String x : xList) {
            int retries = 3
            do {
                int inc = 0
                try {
                    inc = containsA(x)
                    countA.incr(inc)
                    doSomething()
                    break
                } catch (e) {
                    countA.decr(inc)
                    retries--
                }
            } while (retries > 0)
        }
    }

Now no matter the number of times `containsA` is called, the system remains unchanged, at the caller has the ability to now avoid side effects, thus making a more deterministic, reliable system.

Another important reason why we would avoid side effects is if the system depends on a global state that changes when a method is called, there is no easy way to replicate this change across all nodes of the cluster. Global counters on one machine do not hold the values of the counters in another machine.

To be able to process your code in a distributed way, the following pointers will help:<br>
1. Break the data into small immutable blocks which are only transformed from one immutable form to another immutable form<br>
2. Do not transform with side effects - avoid changing the state of the system in the transformations in order to allow retries and reproducible results in a distributed environment.

Often, the reason for an unreliable system yielding non-deterministic results (Different runs of the same data yields different results) is because either the data is mutable or the functions have side effects.

--------

In the next post, let's look at a more complex example, Word Count. [Click here to continue reading.](/2016/01/distributed-thinking-storm-spark-part3 "Part 3")

{% include distthink/navigation.md %}