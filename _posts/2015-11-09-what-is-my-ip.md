---
title: What is my IP?
author: admin
layout: post
permalink: /2015/11/what-is-my-ip/
categories:
  - Java
tags:
  - java
  - ip
  - ip address
  - code
---

I wanted a simple, free, no-limits way of finding out my own public IP. I also wanted a way to use it in my Java code so I wouldn't need a really complex, and non-guaranteed way of finding the IP. I can't believe the number of times I've searched for "How to get my public IP in Java?" or several variations of the same and I was tired of it. 

I wrote a simple server which has one endpoint which returns the IP address of the called. It's a no-frills service that you can host yourself.

You can find the code for this service [here](https://github.com/caffinc/MyIP "MyIP Github") or you could download a pre-compiled jar which can be run on your server [here](https://github.com/caffinc/MyIP/releases/download/v0.1/myip.jar "MyIP jar").

The core of the code, if you want to include in your own Java program is in [this Gist](https://gist.github.com/SriramKeerthi/4fbb8821ad5b75fd0c80):

{% gist 4fbb8821ad5b75fd0c80 %}
