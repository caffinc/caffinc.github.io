---
title: MySQL Command Window Popup
author: admin
layout: post
permalink: /2015/06/mysql-command-window-popup/
categories:
  - MySQL
tags:
  - community edition
  - mysql
  - popup
  - task scheduler
---
I recently installed `MySQL` after a long time. I've been so used to the flexibility that `MongoDB` offers me that I haven't looked at `MySQL` in ages. However I had to install it recently for something I was working on, and chose the `Community Edition` available <a href="http://www.mysql.com/products/community/" target="_blank">here</a>.

Everything went fine. However I was in the middle of doing something else on a different night when I suddenly saw a suspicious popup window that looked something like this:  
![MySQL Community Edition Installer Popup](/assets/images/2015/06/mysqlinstall1.png)

The first time I saw this, I figured it was just some post-installation cleanup that MySQL was performing, and since I hadn't launched it more than once after the first installation, it seemed natural.  
However this popup appeared again, and again I was surprised. I dismissed it a second time thinking 'Oh well, maybe some more updates or something! or maybe it just started up after some long update process.'

The third time it happened, I knew there was something weird. I captured the program and copied the command window text. I searched about it and came across a few places talking about it &#8211; apparently it's a `phone-home updater`. As I don't remember agreeing to having `MySQL` checking every day for changes, I decided to disable this. I followed the instructions <a href="http://forums.mysql.com/read.php?10,626478,626575#msg-626575" target="_blank">here</a>, and thought I'd share it with you for `Windows 8.1`.

First, search for `Schedule Tasks`:  
![Schedule Tasks](/assets/images/2015/06/schedule-tasks.png)

Now expand the `Task Scheduler Library` in the left pane, and expand `MySQL > Installer` under it. You should see an entry named `ManifestUpdate`. Right click this entry and select `Disable`:  
![Schedule Tasks Open](/assets/images/2015/06/schedule-tasks-open1.png)

You're all set! You shouldn't get that task running anymore. At the same time, you might notice a bunch of tasks scheduled to run on your machine which you might choose to disable too. Be careful not to disable something important though!

 [1]: http://caffinc.com/wp-content/uploads/2015/06/mysqlinstall1.png
 [2]: http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks.png
 [3]: http://caffinc.com/wp-content/uploads/2015/06/schedule-tasks-open1.png
