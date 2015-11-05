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
<p style="text-align: left;">
  I recently installed Jenkins on an Amazon instance for work. It was an EC2 instance running Ubuntu. I installed it using:
</p>

<p style="text-align: left; padding-left: 30px;">
  <code>sudo apt-get install jenkins</code>
</p>

By default, Jenkins runs on port 8080. I&#8217;m relatively new to Linux, and I haven&#8217;t quite wrapped my head around configuring different things, like services and init scripts. I decided to search for how to change the default config on Google. I got Jenkins&#8217; page, which was a lot of text I didn&#8217;t want to go through, and I have gone through that several times. I finally managed to snoop around stuff, and found out which file to change:

<p style="padding-left: 30px;">
  <code>sudo vim /etc/default/jenkins</code>
</p>

<div id="attachment_22" style="width: 310px" class="wp-caption alignleft">
  <a href="http://i0.wp.com/caffinc.com/blog/wp-content/uploads/2014/07/jenkins_port_screenshot.png"><img class="size-medium wp-image-22" alt="IT'S OVER 9000! Change the HTTP_PORT value to whatever you need to." src="http://i1.wp.com/caffinc.com/blog/wp-content/uploads/2014/07/jenkins_port_screenshot-300x173.png?fit=300%2C173" data-recalc-dims="1" /></a>
  
  <p class="wp-caption-text">
    IT&#8217;S OVER 9000! Change the HTTP_PORT value to whatever you need to.
  </p>
</div>

&nbsp;

&nbsp;

Now scroll down to the `HTTP_PORT` and change it to whatever number you want to.

&nbsp;

&nbsp;

&nbsp;

Now restart Jenkins using:

<p style="padding-left: 30px;">
  <code>sudo service jenkins restart</code>
</p>

And you should have Jenkins running on whatever port you want!

(Still not sure why I shouldn&#8217;t just change it to port 80, since the help file talks about using Apache to redirect port 8080 to 80. I understand redirecting a subdomain or a /jenkins path, but not changing port 8080 to 80 using Apache.)