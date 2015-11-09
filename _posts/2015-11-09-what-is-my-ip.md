---
title: What is my IP?
author: admin
layout: post
permalink: /2015/11/what-is-my-ip/
categories:
  - Java
tags:
  - web
  - sockets
  - ports
  - redirect
---

I wanted a simple, free, no-limits way of finding out my own public IP. I also wanted a way to use it in my Java code so I wouldn't need a really complex, and non-guaranteed way of finding the IP.

I wrote a simple server which has one endpoint which returns the IP address of the called. It's a no-frills service currently hosted here:
[https://caffinc.com/myip](https://caffinc.com/myip)

You can find the code for this service [here](https://github.com/caffinc/MyIP "MyIP Github") or you could download a pre-compiled jar which can be run on your server [here](https://github.com/caffinc/MyIP/releases/download/v0.1/myip.jar "MyIP jar").

I would like to say that I don't intentionally log any information on my server apart from whatever the server needs to ensure there's no DDOS attacks on it. Please play nice with the service, I don't want to have to ban anyone, or to have my service revoked because someone abused the system.

You can use the code from [my Gist](https://gist.github.com/SriramKeerthi/4fbb8821ad5b75fd0c80) in your Java code:

{% gist 4fbb8821ad5b75fd0c80 %}
