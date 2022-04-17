---
title: Changing the default port on Jenkins
author: admin
layout: post
permalink: /2014/07/changing-the-default-port-on-jenkins/
categories:
  - Jenkins
tags:
  - change
  - default
  - jenkins
  - port
---

I recently installed Jenkins on an Amazon instance for work. It was an EC2 instance running Ubuntu. I installed it using:

```
sudo apt-get install jenkins
```

By default, Jenkins runs on port 8080. I'm relatively new to Linux, and I haven't quite wrapped my head around configuring different things, like services and init scripts. I decided to search for how to change the default config on Google. I got Jenkins' page, which was a lot of text I didn't want to go through, and I have gone through that several times. I finally managed to snoop around stuff, and found out which file to change:

```
sudo vim /etc/default/jenkins
```

![IT'S OVER 9000! Change the HTTP_PORT value to whatever you need to.](/assets/images/2014/07/jenkins_port_screenshot.png)


Now scroll down to the `HTTP_PORT` and change it to whatever number you want to.

Now restart Jenkins using:

```
sudo service jenkins restart
```

And you should have Jenkins running on whatever port you want!

(Still not sure why I shouldn't just change it to port 80, since the help file talks about using Apache to redirect port 8080 to 80. I understand redirecting a subdomain or a /jenkins path, but not changing port 8080 to 80 using Apache.)
