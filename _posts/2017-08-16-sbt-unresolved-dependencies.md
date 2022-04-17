---
title: "SBT complains about unresolved dependencies for multi-module projects"
author: admin
layout: post
permalink: /2017/08/sbt-unresolved-dependencies/
categories:
  - Scala
  - SBT
tags:
  - sbt
  - dependencies
  - scala
  - multimodule
  - build
---
### Or how I learnt a bit more SBT, and continue to be mildly disgruntled by the bomb

## Motivation:

I've wanted to write that "awesome Scala project" for a while now but have been constantly sidetracked by other projects which were more interesting. Since my current project at work doesn't use Scala, it's that much harder to fire up SBT and wait for the Universe to download and build before I can get productive (Incidentally, SBT is a bit faster than what I can remember, so it's probably only downloading about half the Universe now).

I wrote one module of my project and pushed it to Github, and decided that I'd use Travis CI to build it, like I've used it in several projects before. That's when the reason why I dislike comprehensive "shell-in-a-shell" build tools like SBT came rushing in. Whenever a language or build tool encourages using the build tool itself to run your project, I get annoyed. I don't plan to run from source in the live environment, and having to run commands like `sbt run` instead of `java -jar jarhere.jar` worries me. I want the build tool to be just that - build my project. I understand that it's a matter of personal choice and that I *could* build the project and run it using `java -jar`, it annoys me that there are a hundred different tutorials that talk about using `sbt run` but I really have to dig deep to find one that tells me (unambiguously) how to package and run a Jar.

Anyway, the problem was this - what was compiling just fine in my IDE, and what ran just fine inside SBT, was now breaking in Travis CI when I ran the `sbt test` command. This is what I got:

```
x@y:~/project$ sbt test
[info] Loading project definition from /xyz/project
[info] Set current project to projectxyz (in build file:/xyz/)
[info] Updating {file:/xyz/}core...
[info] Resolving common#common_2.12;0.1-SNAPSHOT ...
[warn] 	module not found: common#common_2.12;0.1-SNAPSHOT
[warn] ==== local: tried
[warn]   /home/x/.ivy2/local/common/common_2.12/0.1-SNAPSHOT/ivys/ivy.xml
[warn] ==== public: tried
[warn]   https://repo1.maven.org/maven2/common/common_2.12/0.1-SNAPSHOT/common_2.12-0.1-SNAPSHOT.pom
[warn] ==== local-preloaded-ivy: tried
[warn]   /home/x/.sbt/preloaded/common/common_2.12/0.1-SNAPSHOT/ivys/ivy.xml
[warn] ==== local-preloaded: tried
[warn]   file:////home/x/.sbt/preloaded/common/common_2.12/0.1-SNAPSHOT/common_2.12-0.1-SNAPSHOT.pom
[info] Resolving jline#jline;2.14.4 ...
[warn] 	::::::::::::::::::::::::::::::::::::::::::::::
[warn] 	::          UNRESOLVED DEPENDENCIES         ::
[warn] 	::::::::::::::::::::::::::::::::::::::::::::::
[warn] 	:: common#common_2.12;0.1-SNAPSHOT: not found
[warn] 	::::::::::::::::::::::::::::::::::::::::::::::
[warn] 
[warn] 	Note: Unresolved dependencies path:
[warn] 		common:common_2.12:0.1-SNAPSHOT
[warn] 		  +- core:core_2.12:1.0
sbt.ResolveException: unresolved dependency: common#common_2.12;0.1-SNAPSHOT: not found
	at sbt.IvyActions$.sbt$IvyActions$$resolve(IvyActions.scala:313)
	at sbt.IvyActions$$anonfun$updateEither$1.apply(IvyActions.scala:191)
	at sbt.IvyActions$$anonfun$updateEither$1.apply(IvyActions.scala:168)
	at sbt.IvySbt$Module$$anonfun$withModule$1.apply(Ivy.scala:156)
	at sbt.IvySbt$Module$$anonfun$withModule$1.apply(Ivy.scala:156)
	at sbt.IvySbt$$anonfun$withIvy$1.apply(Ivy.scala:133)
	at sbt.IvySbt.sbt$IvySbt$$action$1(Ivy.scala:57)
	at sbt.IvySbt$$anon$4.call(Ivy.scala:65)
	at xsbt.boot.Locks$GlobalLock.withChannel$1(Locks.scala:93)
	at xsbt.boot.Locks$GlobalLock.xsbt$boot$Locks$GlobalLock$$withChannelRetries$1(Locks.scala:78)
	at xsbt.boot.Locks$GlobalLock$$anonfun$withFileLock$1.apply(Locks.scala:97)
	at xsbt.boot.Using$.withResource(Using.scala:10)
	at xsbt.boot.Using$.apply(Using.scala:9)
	at xsbt.boot.Locks$GlobalLock.ignoringDeadlockAvoided(Locks.scala:58)
	at xsbt.boot.Locks$GlobalLock.withLock(Locks.scala:48)
	at xsbt.boot.Locks$.apply0(Locks.scala:31)
	at xsbt.boot.Locks$.apply(Locks.scala:28)
	at sbt.IvySbt.withDefaultLogger(Ivy.scala:65)
	at sbt.IvySbt.withIvy(Ivy.scala:128)
	at sbt.IvySbt.withIvy(Ivy.scala:125)
	at sbt.IvySbt$Module.withModule(Ivy.scala:156)
	at sbt.IvyActions$.updateEither(IvyActions.scala:168)
	at sbt.Classpaths$$anonfun$sbt$Classpaths$$work$1$1.apply(Defaults.scala:1541)
	at sbt.Classpaths$$anonfun$sbt$Classpaths$$work$1$1.apply(Defaults.scala:1537)
	at sbt.Classpaths$$anonfun$doWork$1$1$$anonfun$121.apply(Defaults.scala:1572)
	at sbt.Classpaths$$anonfun$doWork$1$1$$anonfun$121.apply(Defaults.scala:1570)
	at sbt.Tracked$$anonfun$lastOutput$1.apply(Tracked.scala:37)
	at sbt.Classpaths$$anonfun$doWork$1$1.apply(Defaults.scala:1575)
	at sbt.Classpaths$$anonfun$doWork$1$1.apply(Defaults.scala:1569)
	at sbt.Tracked$$anonfun$inputChanged$1.apply(Tracked.scala:60)
	at sbt.Classpaths$.cachedUpdate(Defaults.scala:1592)
	at sbt.Classpaths$$anonfun$updateTask$1.apply(Defaults.scala:1519)
	at sbt.Classpaths$$anonfun$updateTask$1.apply(Defaults.scala:1471)
	at scala.Function1$$anonfun$compose$1.apply(Function1.scala:47)
	at sbt.$tilde$greater$$anonfun$$u2219$1.apply(TypeFunctions.scala:40)
	at sbt.std.Transform$$anon$4.work(System.scala:63)
	at sbt.Execute$$anonfun$submit$1$$anonfun$apply$1.apply(Execute.scala:228)
	at sbt.Execute$$anonfun$submit$1$$anonfun$apply$1.apply(Execute.scala:228)
	at sbt.ErrorHandling$.wideConvert(ErrorHandling.scala:17)
	at sbt.Execute.work(Execute.scala:237)
	at sbt.Execute$$anonfun$submit$1.apply(Execute.scala:228)
	at sbt.Execute$$anonfun$submit$1.apply(Execute.scala:228)
	at sbt.ConcurrentRestrictions$$anon$4$$anonfun$1.apply(ConcurrentRestrictions.scala:159)
	at sbt.CompletionService$$anon$2.call(CompletionService.scala:28)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
[error] (core/*:update) sbt.ResolveException: unresolved dependency: common#common_2.12;0.1-SNAPSHOT: not found
[error] Total time: 5 s, completed Aug 16, 2017 7:05:59 AM

```

Searching for "sbt unresolved dependencies" was not very useful. So I dug deeper and found the issue.

## Missing build.sbt file, and different version in the root build.sbt

I sometimes need things to be spelled in caps and in a very large font to catch my attention :) While the rest of my modules had an explicit `build.sbt` file in the nested structure:

```
.
+-- build.sbt
+-- common
    \-- src
        \-- main
            \-- scala
                \-- com
                    \-- caffinc
                        \-- xyz
+-- core
    +-- build.sbt
    \-- src
        \-- main
            \-- scala
                \-- com
                    \-- caffinc
                        \-- xyz
+-- project
    +-- build.properties
    \-- plugins.sbt
+-- target
\-- web
    +-- app
        \-- com
            \-- caffinc
                \-- xyz
    +-- build.sbt
    \-- conf
```

My `common` project did not have a `build.sbt`. In my root `build.sbt`, the `core` project depends on the `common` project:

```
name := "xyz"

version := "0.1"

scalaVersion := "2.12.3"

lazy val common = project

lazy val core = project
  .dependsOn(common)

lazy val web = project
  .dependsOn(common)
```

My `core/build.sbt` looks like this:

```
version := "1.0"

scalaVersion := "2.12.3"

libraryDependencies ++= Seq(
  "org.apache.kafka" %% "kafka" % "0.11.0.0"
)
```

I went ahead and added an explicit `build.sbt` for `common`, and this fixed my build issues. I changed the version in my root `build.sbt` to "1.0" cause *whoops*.

## TL;DR

Check your damn root `build.sbt` file's `version` entry. Add an explicit `build.sbt` inside your sub-modules.

I hope this helps someone out there who's trying to come to terms with `SBT`!
