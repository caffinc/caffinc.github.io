---
title: Installing InfluxDB as a service on Ubuntu without systemd
author: admin
layout: post
permalink: /2015/12/influxdb-service-ubuntu/
comments: true
categories:
  - InfluxDB
  - Ubuntu
  - Service
tags:
  - influxdb
  - service
  - ubuntu
  - gce
  - systemd
  - systemctl
---

<img src="https://customers.influxdb.com/assets/influxdb-light400-f0158e32e3e19795f9c19e572b491b6f6b7a9a4856469ee254df5872f31a501d.png" alt="InfluxDB Logo" style="width: 400"/>

I was looking for a good system to benchmark my application recently, and [InfluxDB](https://influxdb.com/ "InfluxDB") combined with [Grafana](http://grafana.org/ "Grafana") seemed like a really good solution. I decided to set it up on my Google Compute Engine instance. I must note that using a `Docker` image would probably be much, much simpler than installing components individually. I highly recommend going the [Docker way](https://hub.docker.com/r/alexcheng/docker-grafana-influxdb/ "Docker image with InfluxDB and Grafana").

I installed InfluxDB as shown on [their site](https://influxdb.com/docs/v0.9/introduction/installation.html "InfluxDB Installation") on one of my machines and things worked just as mentioned on the site. However, on my other Google Compute Engine instance, it didn't. InfluxDB did not install as a service, and I couldn't get it to start as a service either.

I tried using `systemctl`:

    $ sudo systemctl start influxd
    Failed to get D-Bus connection: No connection to service manager.


I searched about this issue on many sites (like [this](http://www.linuxquestions.org/questions/linux-software-2/systemctl-failed-to-get-dbus-connection-no-connection-to-service-manager-4175466325/) and [this](https://bbs.archlinux.org/viewtopic.php?id=149288)) and concluded that `systemctl` would be of no use to me.

I decided to add a script for InfluxDB myself. I started looking for any files on the web that matched `/etc/init.d/influxd` and couldn't find anything. After digging around I noticed that the [`init.sh` script in the InfluxDB GitHub Repository](https://github.com/influxdb/influxdb/blob/master/scripts/init.sh) looked a lot like an `LSB` script. I decided to try it out. I created a new file:

    sudo vim /etc/init.d/influxd

I copied the `init.sh` script linked above into this file and set it as an executable:

    sudo chmod +x /etc/init.d/influxd

Now I could start up InfluxDB as a service! I started it using:

    $ sudo service influxd start
    Starting the process influxdb [ OK ]
    influxdb process was started [ OK ]

    $ ps -ef|grep influx
    influxdb  8406     1  0 08:58 pts/11   00:00:00 /usr/bin/influxd -pidfile /var/run/influxdb/influxd.pid -config /etc/influxdb/influxdb.conf
    
    $ curl -i 'http://localhost:8086/ping'
    HTTP/1.1 204 No Content
    Request-Id: fcdd016c-98d2-11e5-8001-000000000000
    X-Influxdb-Version: 0.9.5.1
    Date: Wed, 02 Dec 2015 08:59:25 GMT

    $ sudo service influxd status
    influxdb Process is running [ OK ]

Everything seems to be working just fine! Hope this helps someone else struggling to start InfluxDB as a service on a machine that isn't booting with `systemd`, and InfluxDB isn't installing as a service as expected.
