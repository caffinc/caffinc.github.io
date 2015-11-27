---
title: Amazon SQS powered Alerts system in Java
author: admin
layout: post
permalink: /2015/11/amazon-sqs-alerts/
comments: true
categories:
  - Amazon
  - AWS
  - Java
tags:
  - amazon
  - aws
  - sqs
  - java
  - alerts
---
<img src="http://caffinc.com/wp-content/uploads/2015/11/amazon-aws-logo.png" alt="Amazon AWS Logo" style="width: 200px;"/><img src="http://caffinc.com/wp-content/uploads/2015/11/amazon-sqs-logo.png" alt="Amazon AWS Logo" style="width: 100px;"/>

[Amazon AWS](https://aws.amazon.com "Amazon AWS") is pretty cool, and everyone knows that. Recently my manager asked me to look at `Amazon SQS` for our alerts and notifications.

I searched on the [official SQS site](https://aws.amazon.com/sqs/ "Amazon SQS official site") for documentation and found a link to their [GitHub page](https://github.com/aws/aws-sdk-java/tree/master/src/samples/AmazonSimpleQueueService "Amazon SQS GitHub example") which had a Java example.

I next checked the pricing and Amazon has some [really good pricing on the SQS](https://aws.amazon.com/sqs/pricing/ "Amazon SQS Pricing") as well. A million requests free every month and about $0.50 for another million.

I saw that Amazon wanted me to "install" the `Amazon AWS SDK` onto my local machine to get started. I **hate** software that assumes that I need them forever and ever. Especially an SDK which isn't integrated to my IDE, but rather something that I need a little module out of - namely the SQS module. I searched for "Amazon AWS SDK Maven Plugin" and came up with [this](http://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk "Maven Repository for Amazon AWS SDK"). (*Disclaimer:* I never bothered to check what "install" meant, it might not be as scary as I think it is.)

I created a new `Maven` project, and added the Amazon AWS SDK dependency (Released on the 25th, 2 days ago!):
    
    <dependency>
    	<groupId>com.amazonaws</groupId>
    	<artifactId>aws-java-sdk</artifactId>
    	<version>1.10.37</version>
    </dependency>
    
I then copied over the code from GitHub but it didn't work, namely because the version of the SDK they were using in the code was not the same as the SDK available on Maven. I went back to my `pom.xml` and noticed that I'd imported `v1.0.001`, which is a really old version of the SDK. 

I downloaded the latest version (mentioned above) and everything was compiling!

I now needed to get an `access key` and `secret key` for using the SDK. I searched around and [found a few pages](http://blogs.aws.amazon.com/security/post/Tx1R9KDN9ISZ0HF/Where-s-my-secret-access-key "Amazon Blog post on Secret Access Keys") which told me that I had to do the following:

1. Go to the [Identity and Access Management Dashboard](https://console.aws.amazon.com/iam/home?#home "Amazon IAM home")
2. Create a new Group from the `Groups` option on the left pane and attach the `AmazonSQSFullAccess` policy
3. Add a user to this group
4. Go to the `Users` option and select the user added. This should take you to a page with tabs labeled `Groups`, `Permissions` and `Security Credentials`. Select the `Security Credentials` tab and you can find your Access Keys there! Create one if you don't have one already.

Now copy them over to the location specified in the Java example and Amazon should let you use the SQS service!

I decided to modify the code to make it into an independent module that I can plug into my other projects, and let me have a clean way to access the SQS system. I've uploaded the [project to GitHub](https://github.com/caffinc/alerts "Alerts module"), feel free to use it in your code! It's a one-class module, with ample (Or so I think) tests.

**Note:**
There are some issues I faced while testing the SQS service:

1. Exception isn't thrown immediately if the credentials aren't correct, but it is thrown only when you try to do something with the SQS, like `listQueues()` or make a request.
2. A queue isn't created immediately when you request it to be created, but may take a few seconds. The only way I could guarantee that it was created for my testing was to actually send a message on the queue and force SQS to create the queue for me. If I didn't do this, the `listQueues()` method was very sporadic.
3. Deleting a queue takes up an arbitrary amount of time. The queue will show up in the `listQueues()` call even after it has been deleted. Sending a message on a deleted queue results in an exception however, so that can be used to check if the queue has actually been deleted or not.
4. Creating a new queue with the same name as a deleted queue will throw an exception if the queue was deleted less than 60 seconds before creating it again.

Overall I'm happy with how easy it was to get SQS to work for me! Good luck!
