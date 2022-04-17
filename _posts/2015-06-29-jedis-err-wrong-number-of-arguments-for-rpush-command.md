---
title: 'Jedis: ERR wrong number of arguments for &#8216;rpush&#8217; command'
author: admin
layout: post
permalink: /2015/06/jedis-err-wrong-number-of-arguments-for-rpush-command/
categories:
  - Java
  - Redis
tags:
  - Exception
  - java
  - JedisDataException
  - Redis
---
While refactoring a large project with some legacy unmanageable code which used Redis, I came across this exception:

    Exception in thread "main" redis.clients.jedis.exceptions.JedisDataException: ERR wrong number of arguments for 'rpush' command
        at redis.clients.jedis.Protocol.processError(Protocol.java:59)
        at redis.clients.jedis.Protocol.process(Protocol.java:66)
        at redis.clients.jedis.Protocol.read(Protocol.java:131)
        at redis.clients.jedis.Connection.getIntegerReply(Connection.java:188)
        at redis.clients.jedis.Jedis.rpush(Jedis.java:873)
        at com.caffinc.RedisCacheManager.storeDoubleList(RedisCacheManager.java:276)
    

It was intermittent and used to appear only sometimes, and used to mess up the process. After pulling my hair out for a while, I discovered that **passing an empty list** to `jCon.rpush( name, values );` causes this exception. Rather weird if you ask me. Empty lists are completely logical, and in most cases necessary. I handled this by simply checking if the list was empty before inserting into `Redis`.

I thought this would be all, as the problem did disappear for a while. But when I checked in my code after making some major refactoring, the problem returned! Try as I might, it this call kept failing and everything seemed fine on my machine locally&#8230;

I realized I&#8217;d upgraded `Redis` on my machine, and also `Jedis` in my code. I upgraded `Redis` from `2.2` on the server to `3.0.2` and things were working fine again!