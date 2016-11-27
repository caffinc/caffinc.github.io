---
title: "Going to Maven Central!"
author: admin
layout: post
permalink: /2016/04/going-to-maven-central/
categories:
  - Java
  - API
  - Jersey
tags:
  - java
  - jersey
  - api
  - injection
---

I DONE DIDDLY DID IT! I got my first few JARs on Maven Central! I'm officially one of 'em new town boys now, ma! Lookit me fancy shoes!

![https://upload.wikimedia.org/wikipedia/commons/e/ed/Jack_Delano,_Going_to_town_on_Saturday_afternoon,_Greene_County,_Georgia,_1941.jpg](https://upload.wikimedia.org/wikipedia/commons/e/ed/Jack_Delano,_Going_to_town_on_Saturday_afternoon,_Greene_County,_Georgia,_1941.jpg)

Apart from putting my personal work there, I've also configured some of my company's open source contributions to get pushed onto Maven Central, and the feeling is great!

Did I have to sell my soul for it? Not at all. Wait... I mean... Not all of it.

I first made a deal with the devil, [Bintray](https://blog.bintray.com/2014/02/11/bintray-as-pain-free-gateway-to-maven-central/). They're an awesome place to put all your JARs and stuff, and quickly too. Within a day you can have your JAR sitting snugly next to big-name brands in JCenter, a central repository (supposedly) much larger than Maven Central.

After that, you'll have to sign up at [Sonatype](http://central.sonatype.org/pages/ossrh-guide.html). Once in, you have to create a new issue for your first submission into a particular Group ID.

Once you've been accepted as the publisher for your group ID, you can go back to Bintray and select your project. You should see a `Maven Central` tab on the top right, next to the `Statistics` and `Files` tabs. Select this option. You should see something like this:

![http://caffinc.github.io/images/bintray_maven_sync.png](http://caffinc.github.io/images/bintray_maven_sync.png)

Enter your credentials from Sonatype here and click `Sync`. If all goes well, your project should sync up and show up in Maven Central in a day or two. If not, you'll probably get a completely useless error message.

I noticed that I had to put the following in my pom files for it to work. I'll use my `jaggr` project as an example below:

1. Project Info:

I had to include some textual information about my project:

	<name>jaggr</name>
    <description>Simple JSON Aggregator for Java</description>
    <url>https://github.com/caffinc/jaggr</url>

2. Developer Info:

Developer information was required for it to work correctly on Bintray and Maven:

    <developers>
        <developer>
            <name>Caffinc</name>
            <email>admin@caffinc.com</email>
            <organization>Caffinc</organization>
            <organizationUrl>http://www.caffinc.com</organizationUrl>
        </developer>
    </developers>

3. License Info:

Without License Information, even if you provide it using other means, the sync fails:

    <licenses>
        <license>
            <name>MIT License</name>
            <url>http://www.opensource.org/licenses/mit-license.php</url>
            <distribution>repo</distribution>
        </license>
    </licenses>

4. SCM Info:

This I'm not really sure about. But I put it in just to be safe:

    <scm>
        <url>https://github.com/caffinc/jaggr</url>
    </scm>

5. Source and Javadoc

While you don't really need the following in your pom file if you have a better way to do it, but I found that adding a couple of plugins to get the source jar and the javadoc jar was very helpful, especially because I found it easier to just hand over my source and javadoc as a jar instead of wasting a few hours figuring out how to avoid that. In any case, the source is on Github.
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-javadoc-plugin</artifactId>
                <executions>
                    <execution>
                        <id>attach-javadocs</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    

Oh, and one last thing - that "pom file" that Bintray cries about. Took me a few minutes to realize that it just wants me to copy the `pom.xml` file to a `project-version.pom` file and upload it (E.g. `jaggr-0.1.pom`).

Hope this helps you out!

-------

Edit: I originally wrote a bulk of this in April, and only bothered completing it in November. I'm going to get back to adding an article every couple of weeks again!