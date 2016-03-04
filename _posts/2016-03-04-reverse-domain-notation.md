---
title: "Reverse domain notation" 
author: admin
layout: post
permalink: /2016/03/reverse-domain-notation/
comments: true
categories:
  - Scala
  - Java
  - MongoDB
tags:
  - scala
  - java
  - mongo
  - mongodb
  - bson
---

<img src="https://www.mongodb.com/assets/MongoDB_Brand_Resources/MongoDB-Logo-7db53b4037f9953e9df4f694c758141fcfa022cee1d2ff50749d133d0b70b692.jpg" alt="MongoDB Logo" style="height: 100px"/>

I've been trying to wrap my head around getting my jar into Maven Central. The process used to be easier, and now it's a lot of paperwork and fineprint.

One of the things I read as a requirement for being able to submit your jar to Maven Central is that it should have a `groupId` which is in [`Reverse Domain Name Notation`](https://en.wikipedia.org/wiki/Reverse_domain_name_notation). And not just that, the domain should also be something you control.

While this is all fine, my overworked brain decided to check out some of the packages to see if the domains actually existed. One of the domains I picked was `org.bson`. So I went on down to [bson.org](http://bson.org).

I quickly discovered that not only are package names not guaranteed to be under the control of the package creator (In this case MongoDB), but there are parts of the Internet that's been lost in time but still manages to survive. Bson.org was created way back in 1999, when I first discovered the Internet and decided that I needed an email account with "99" in it. I quickly came to despise email IDs which contain any form of dating information.

![bson.org homepage](https://caffinc.github.io/images/bson.org.png)

Not only is the website designed in line with the quick-and-dirty Frontpage or bare early HTML websites of yesteryear, it's somehow been kept alive for over 16 years now!

Either the person who owns the website really loves it, or is just trolling the guys who're working on the BSON spec. Either way, it's a quick glimpse into how far we've come in terms of web technologies, and what passes for great design.

What have you learned today? 