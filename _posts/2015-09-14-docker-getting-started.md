---
title: 'Docker on Windows 10: Getting started'
author: admin
layout: post
permalink: /2015/09/docker-getting-started/
videourl:
  - 
shareaholic_disable_recommendations:
  - 1
categories:
  - API
  - Containerization
  - Docker
  - Java
tags:
  - containerization
  - containers
  - docker
  - hardware acceleration
  - hyper-v
  - virtualbox
---
For a recent project we decided to try out containerization, so I thought it&#8217;d be good to get my feet wet by playing around with <a href="https://www.docker.com/" target="_blank">Docker</a>.  
![Docker Logo][1]

Docker is pretty cool, and I&#8217;ve seen it used in a project I worked on before. I thought I&#8217;d set it up on my Windows 10 machine, and ran into a couple of issues:

  1. Docker needs VirtualBox  
    I already had VirtualBox installed, and the <a href="https://www.docker.com/toolbox" target="_blank">Docker Toolbox</a> came with its own installer for VirtualBox. I ignored VirtualBox while installing, and somehow Docker didn&#8217;t talk nicely with my existing VirtualBox. I reinstalled with VirtualBox, and things seemed to be working.</p> 
  2. Hardware Virtualization  
    Docker needs a 64bit guest OS, which for some reason my Virtualization enabled processor still wasn&#8217;t playing nice with. I kept getting errors that stated `"vt-x/amd-v hardware acceleration is not available"`. I discovered that the problem was `Hyper-V`, which wouldn&#8217;t let VirtualBox discover the hardware virtualization. I disabled this by going to the `Control Panel > Programs > Turn Windows features on or off` and un-checking `Hyper-V` in the list shown.  
    [<img src="http://caffinc.com/wp-content/uploads/2015/09/hyperv.png?fit=752%2C592" alt="Disable Hyper-V from the Control Panel" class="aligncenter size-full wp-image-108" data-recalc-dims="1" />][2]

I started Docker again using the `Docker Quickstart Terminal` and this time everything worked as planned.

P.S. I&#8217;m following the instructions <a href="http://docs.docker.com/windows/started/" target="_blank">here</a> to learn Docker. Good luck with your containerization efforts!

 [1]: https://www.docker.com/sites/all/themes/docker/assets/images/logo.png
 [2]: http://caffinc.com/wp-content/uploads/2015/09/hyperv.png
