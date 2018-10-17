---
title: MySQL Command Window Popup
author: admin
layout: post
permalink: /2015/06/mysql-command-window-popup/
videourl:
  - 
categories:
  - MySQL
tags:
  - community edition
  - mysql
  - popup
  - task scheduler
---
I recently installed `MySQL` after a long time. I&#8217;ve been so used to the flexibility that `MongoDB` offers me that I haven&#8217;t looked at `MySQL` in ages. However I had to install it recently for something I was working on, and chose the `Community Edition` available <a href="http://www.mysql.com/products/community/" target="_blank">here</a>.

Everything went fine. However I was in the middle of doing something else on a different night when I suddenly saw a suspicious popup window that looked something like this:  
[<img src="http://caffinc.com/wp-content/uploads/2015/06/mysqlinstall1.png?fit=677%2C343" alt="MySQL Community Edition Installer Popup" class="aligncenter size-full wp-image-89" data-recalc-dims="1" />][1]

The first time I saw this, I figured it was just some post-installation cleanup that MySQL was performing, and since I hadn&#8217;t launched it more than once after the first installation, it seemed natural.  
However this popup appeared again, and again I was surprised. I dismissed it a second time thinking &#8220;Oh well, maybe some more updates or something&#8230; or maybe it just started up after some long update process.&#8221;

The third time it happened, I knew there was something weird. I captured the program and copied the command window text. I searched about it and came across a few places talking about it &#8211; apparently it&#8217;s a `phone-home updater`. As I don&#8217;t remember agreeing to having `MySQL` checking every day for changes, I decided to disable this. I followed the instructions <a href="http://forums.mysql.com/read.php?10,626478,626575#msg-626575" target="_blank">here</a>, and thought I&#8217;d share it with you for `Windows 8.1`.

First, search for `Schedule Tasks`:  
[<img src="http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks.png?fit=346%2C411" alt="Schedule Tasks" class="aligncenter size-full wp-image-87" data-recalc-dims="1" />][2]

Now expand the `Task Scheduler Library` in the left pane, and expand `MySQL > Installer` under it. You should see an entry named `ManifestUpdate`. Right click this entry and select `Disable`:  
[<img src="http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks-open1.png?fit=788%2C481" alt="Schedule Tasks Open" class="aligncenter size-full wp-image-93" data-recalc-dims="1" />][3]

You&#8217;re all set! You shouldn&#8217;t get that task running anymore. At the same time, you might notice a bunch of tasks scheduled to run on your machine which you might choose to disable too. Be careful not to disable something important though!

 [1]: http://caffinc.com/wp-content/uploads/2015/06/mysqlinstall1.png
 [2]: http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks.png
 [3]: http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks-open1.png
