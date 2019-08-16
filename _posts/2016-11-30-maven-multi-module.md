---
title: "Submitting Multi-Module artifacts to Maven: My JAR does not download!"
author: admin
layout: post
permalink: /2016/11/maven-multi-module/
categories:
  - Maven
  - Bintray
tags:
  - maven
  - pom
  - artifact
---

# Submitting Multi-Module artifacts to Maven: My JAR does not download!

## The situation:

I just started work on creating a new JSON Aggregation framework in Java. My goal is to create a scalable-yet-simple aggregation framework which supports aggregation of arbitrary JSON objects inside Java, similar to the [Aggregation Framework provided by MongoDB](https://docs.mongodb.com/v3.2/aggregation/), but perhaps not as complex, but supporting larger datasets.

After completing the first few features, I uploaded [`jaggr` to Bintray](https://bintray.com/caffinc/maven/jaggr). You can add it as a Maven dependency to your project:
	
	<dependency>
	  <groupId>com.caffinc</groupId>
	  <artifactId>jaggr</artifactId>
	  <version>0.5.0</version>
	</dependency>

The first couple of versions (`0.1`, `0.2`) worked just fine and I could add it as a dependency in my other projects, and I was happy. However starting from version `0.3.0`, I split the project into `jaggr-parent`, `jaggr` and `jaggr-utils` to help the project grow better, separating out the core aggregation framework from the accompanying plumbing (File readers, iterators, object iterators, etc.)

I submitted the project to Bintray and then to Maven Central through Bintray, and everything worked fine. I added it as a dependency to my project and things *seemed* to be OK.

But they weren't. Though `jaggr` got added as a dependency successfully, the corresponding JAR files never got downloaded to my local Maven cache. Which meant that I couldn't actually use it. Maven was only downloading the POM file but not the JAR. I tried downloading several times, clearing the cache repeatedly. I finally found out that ever since I changed versions from `0.2` to `0.3.0`, the JARs stopped downloading.

I searched about why this might happen on Google. Several variations of "Maven JAR does not download", "submit multi-module projects to Maven Central" and "Maven downloads only POM" later, I gave up on finding a solution online. I suspected it was PEBKAC, owing to the fact that I like trying things out and watching it break vs reading walls of text (like this one) and doing things the right way.

## The diagnosis:

Something about the way these JARs were submitted to Maven and Bintray wasn't right and I was missing something. Since the problem started ever since I split the project three ways (four ways, if you count the new benchmark module that isn't submitted to Maven), I came to the conclusion that I didn't submit things right. I opened the Maven cache folder and saw that Maven was able to download the POM for `jaggr`, and also attempted to download `jaggr-parent`, but `jaggr-parent` didn't have a POM - because I didn't upload it. Never occurred to me that that might be relevant to the module. Rather obvious now if you ask me.

I checked if this behavior was common. Downloading only `jaggr-utils` triggered the `jaggr-parent` download as well. I decided to check some other repositories in my cache. I found Google's `Guava`. Opening the folder showed me a folder called `guava-parent`. I came to the conclusion that this was expected behavior and I was a little dumb in only submitting the JARs and POMs I thought were relevant.

## The cure:

I uploaded the `jaggr-parent` POM for the latest version at the time of this writing (0.5.0) to Bintray and synced it to Maven central and waited the rule-of-thumb 10 minutes it takes to sync, and tried again. This time it worked! With my parent POM downloading, Maven resolves dependencies and artifacts correctly and downloads the JAR for `jaggr` as expected!

## TL;DR

If you're uploading multi-module projects to Bintray and/or Maven Central, **make sure that you upload your parent POM file as well**, or your artifacts will appear to be submitted correctly, and the JARs will be available on Maven, but they will never download the way they should.

------------

Hope this spares someone some headache in the future, or serves as a bit of learning for myself about the intricacies of Maven dependencies and artifact management. As always, if you have an issue with something in the post, or with something you think I can help you with, send me an email to admin@caffinc.com and I'll try to help you out!
 
