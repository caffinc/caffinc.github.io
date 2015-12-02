---
title: "Amazon SQS - java.lang.NoSuchFieldError: INSTANCE"
author: admin
layout: post
permalink: /2015/12/sqs-instance-exception/
comments: true
categories:
  - Amazon
  - AWS
  - Java
  - Apache
tags:
  - amazon
  - aws
  - sqs
  - java
  - alerts
  - http
  - instance
  - exception
---

<img src="http://caffinc.com/wp-content/uploads/2015/11/amazon-aws-logo.png" alt="Amazon AWS Logo" style="width: 200px;"/><img src="http://caffinc.com/wp-content/uploads/2015/11/amazon-sqs-logo.png" alt="Amazon AWS Logo" style="width: 100px;"/>

My [previous post](/2015/11/amazon-sqs-alerts/ "Amazon SQS Alerts") discussed about creating an Alerts system in Java using an `Amazon SQS` backend.

Everything worked great on my machine (Except for some non-determinism in the behavior of SQS). However a colleague of mine decided to use the same bit of code and her machine would throw up this error:
    
    Exception in thread "main" java.lang.NoSuchFieldError: INSTANCE
       at com.amazonaws.http.conn.SdkConnectionKeepAliveStrategy.getKeepAliveDuration(SdkConnectionKeepAliveStrategy.java:48)
       at org.apache.http.impl.client.DefaultRequestDirector.execute(DefaultRequestDirector.java:533)
       at org.apache.http.impl.client.AbstractHttpClient.execute(AbstractHttpClient.java:906)
       at org.apache.http.impl.client.AbstractHttpClient.execute(AbstractHttpClient.java:805)
       at com.amazonaws.http.AmazonHttpClient.executeOneRequest(AmazonHttpClient.java:819)
       at com.amazonaws.http.AmazonHttpClient.executeHelper(AmazonHttpClient.java:574)
       at com.amazonaws.http.AmazonHttpClient.doExecute(AmazonHttpClient.java:362)
       at com.amazonaws.http.AmazonHttpClient.executeWithTimer(AmazonHttpClient.java:328)
       at com.amazonaws.http.AmazonHttpClient.execute(AmazonHttpClient.java:307)
       at com.amazonaws.services.sqs.AmazonSQSClient.invoke(AmazonSQSClient.java:2419)
       at com.amazonaws.services.sqs.AmazonSQSClient.listQueues(AmazonSQSClient.java:1179)
       at com.amazonaws.services.sqs.AmazonSQSClient.listQueues(AmazonSQSClient.java:2131)
       ...
    

It was happening in a bit of code that I had no control over. I didn't even know exactly what this issue was. I searched for it and there wasn't anything that matched SQS with this exception.
I finally realized that this was a problem with the version of `Apache HTTP Client` that was on my colleague's machine. She had a dependency on `v4.2` of the Apache HTTP Client in her code, while the Amazon AWS SDK used `v4.3.6`, which I found using a `mvn dependency:tree` command:

    $ mvn dependency:tree
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] Building CommonAlerts 1.0.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO]
    [INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ common-alerts ---
    [INFO] com.caffinc:common-alerts:jar:1.0.0-SNAPSHOT
    [INFO] +- com.amazonaws:aws-java-sdk:jar:1.10.37:compile
    [INFO] |  +- com.amazonaws:aws-java-sdk-support:jar:1.10.37:compile
    ...
    [INFO] |  +- com.amazonaws:aws-java-sdk-api-gateway:jar:1.10.37:compile
    [INFO] |  \- com.amazonaws:aws-java-sdk-core:jar:1.10.37:compile
    [INFO] |     +- commons-logging:commons-logging:jar:1.1.3:compile
    [INFO] |     +- org.apache.httpcomponents:httpclient:jar:4.3.6:compile
    [INFO] |     |  +- org.apache.httpcomponents:httpcore:jar:4.3.3:compile
    [INFO] |     |  \- commons-codec:commons-codec:jar:1.6:compile
    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 4.367 s
    [INFO] Finished at: 2015-12-01T17:06:41+05:30
    [INFO] Final Memory: 13M/244M
    [INFO] ------------------------------------------------------------------------

We updated her code to use `v4.3.6` instead and everything worked just fine! Hope this helps someone else in need!
 