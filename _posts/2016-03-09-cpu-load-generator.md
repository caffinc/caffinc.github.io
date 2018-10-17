---
title: "CPU Load Generator in Java"
author: admin
layout: post
permalink: /2016/03/cpu-load-generator/
categories:
  - Java
  - CPU
tags:
  - java
  - cpu
  - load generator
  - load
---
<h1>Introduction</h1>

While testing some of our distributed systems, I wanted a quick way to generate some load on each node. This can be used to test auto-scaling of the machine by creating loads that trigger new nodes to be launched.

I thought this was an interesting problem to solve, and went about creating `Grex`, an open-source distributed simple CPU load generator written in Java. Grex comes from the Latin noun meaning "flock", or in this case, a group of load generators.

You can grab it from [here](https://github.com/caffinc/grex "Grex"), but it might not be ready yet (I might still be working out the details). However, the core part of Grex is the `BusyThread`, a Java thread which corresponds to one CPU thread. I have made that code available below.

<h1>Basic Idea</h1>

The basic idea is this:

1. Find out the number of cores, `N`.
2. Find out the number of threads per CPU core, `M`.
3. Find out the number of CPU threads, `C = N*M`.
4. Launch `C` new `BusyThread`s.
5. Keep the `BusyThread`s busy for the given % load, and the whole CPU will effectively have a load equal to the given load %.

Keep in mind that this is not perfect, but the loads generated were within +/- 3% on my machine, which uses Intel's ReadyBoost and keeps screwing up the actual load on the machine.

<h1>Code</h1>

An example class, called `Load.java` can be found in [my Gist](https://gist.github.com/SriramKeerthi/0f1513a62b3b09fecaeb), feel free to use it:

{% gist 0f1513a62b3b09fecaeb %}

<h1>Why it works</h1>

The basic assumption is that an "infinite loop" in Java takes up the whole CPU thread. If a given CPU has `N = 4` cores, and `M = 2` threads per core, then one thread in Java running an infinite loop generates a load of `1/(4*2) = ~12.5%`.

Now if we launch 8 threads, we're effectively doing `8/(4*2) = ~100%` load. To keep the load even, we need to distribute it to all threads, so we launch `N*M` threads, and keep them busy for the % duration equal to the load.

If a thread has to load the CPU for 50%, then it runs like an infinite loop for 50ms every 100ms, and sleeps for the other 50ms. If it has to load the CPU for 25%, it runs like an infinite loop for 25ms every 100ms, and sleeps for the other 75ms.

<h1>Conclusions</h1>

This system isn't perfect. The granularity is only 100ms, or 1/10th of a second. Given a long enough duration where 1/10th of a second makes an insignificant portion, this code works fine. However, various factors may cause the code to not generate the exact load expected of it.

For example, you may have a process running that already occupies 10% of the CPU. If this process is multi-threaded, it might be occupying 10% of the CPU across several CPU threads. If it's single-threaded, and you have 8 CPU threads, it might be occupying nearly 100% of one thread.

This system makes a best-attempt to generate the specified load on an unladen machine, but it might be a little inaccurate.

I hope Grex or this code helps you in your testing endeavours! Feel free to reach out to me if there's some issue with the code or if you'd like to contribute to Grex.
