---
title: Get Jetty Server port
author: admin
layout: post
permalink: /2015/11/jetty-server-port/
categories:
  - Java
  - Jetty
tags:
  - web
  - jetty
  - server
  - embedded
  - ports
---

I was working on a project where I had a central load-sharing server with multiple nodes to serve data from. I wanted the project to auto-scale, i.e. bring up new instances by starting jars which connect to the load-sharing server and let the server start sending requests to it.

The problem was that I wanted to start multiple serving nodes on one machine, and they would have a port conflict. The way to make this work was to let the node start on a random port, and let `Jetty` decide the port for us. But getting *which* port `Jetty` decided to give us is not straightforward. The simplest most straight-forward way to get the port that I found was this:

    ( (ServerConnector) server.getConnectors()[0] ).getLocalPort();

This identifies the first connector running on the server, and casts it to a `ServerConnector` which provides the method `getLocalPort()` which is the port the server is listening on.

I love using `embedded Jetty` to build fat jars which serve the whole module in a neat dependency-free jar file that I can copy and run anywhere, and this lets me toss jars onto several servers and just start them there and have my system *just work*!
