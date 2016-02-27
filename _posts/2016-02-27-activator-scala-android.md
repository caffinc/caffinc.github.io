---
title: "Building Android apps in Scala using Activator Templates on Windows 10" 
author: admin
layout: post
permalink: /2016/02/activator-scala-android/
comments: true
categories:
  - Scala
  - Android
  - Activator
tags:
  - scala
  - activator
  - android
  - proguard
  - windows 10
  - app
---

So, like me, you decided to combine Scala and Android and get your hands dirty. If, like me, you're on Windows and things aren't working the way they should, maybe I can help!

I'm going to help you get started from level 0 up to the point where you can get your app to show up on a connected Android device.

Let me break this post down to smaller parts so you can pick up where you are:

1. Setting up IntelliJ to develop in Scala
2. Installing Android SDKs
3. Installing required plugins
4. Creating your first app
5. Troubleshooting
6. Conclusion


What motivated me to write this post was mostly point #5, so I'll be as quick as I can with the other details.

<h2>1. Setting up IntelliJ to develop in Scala</h2>

IntelliJ IDEA is one of those IDEs that has made me enjoy developing on Java. I was primarily a C# developer spoiled on Visual Studio's quick, clean and powerful environment. Developing on Eclipse annoyed me as it would be a memory hog requiring frequent restarts (At least for me on my setup. YMMW.)

IntelliJ IDEA is a great IDE to write Scala code in, and a great place to get started on our Scala Journey.

Install IntelliJ IDEA from [here](https://www.jetbrains.com/idea/download/#section=windows "IntelliJ IDEA Downloads") (You only need the Community edition, but feel free to buy the Ultimate edition instead). Once you have that installed, one of the first things you're prompted to is to install Scala, which is one of the most popular plugins on IntelliJ. If you missed your chance there, you can also install IntelliJ by going to your `File > Settings`. Select the `Plugins` option on the left and then click the `Install JetBrains plugin...` button. In the pop-up window that shows up, select `Sort by: downloads`. `Scala` should be the first one. Or you can search for Scala as well.

<img src="http://eng.trueaccord.com/wp-content/uploads/2014/10/scala-logo.png" alt="Scala Logo" style="height: 100"/><img src="http://s18.postimg.org/6gcq0qtih/intellij_idea_logo_400x400.png" alt="IntelliJ IDEA Logo" style="height: 100"/>

That is all. This will download all required dependencies on the fly as you create a new project. I did the mistake of downloading Scala directly and have suffered the consequences of multiple installations driving me slightly senile.

You would still have to download `SBT` from [here and install it](http://www.scala-sbt.org/download.html "SBT Downloads"). **Make sure you install it to some location that doesn't have spaces in the path.** I cannot stress this enough because some libraries have a tough time working with paths with spaces in them. Now add `SBT` to your path and you should be ready to do all your Scala work for now.

<h2>2. Installing Android SDKs</h2>

While Android Studio is a great way to get started on Android development, I'm not sure how good it is for Scala in particular. Going to IntelliJ + Scala route was the one I thought made more sense, you might want to do it differently.

<img src="https://upload.wikimedia.org/wikipedia/commons/6/66/Android_robot.png
" alt="Android Logo" style="height: 100"/>

Download the `SDK Tools Only` installer from [here](http://developer.android.com/sdk/index.html#Other "Android SDK"). Pick the one most appropriate for you.

**While installing, remember that it is crucial that you don't install it in a folder that contains spaces.** This will save you unnecessary pain later, when plugins like ProGuard refuse to work and spit out cryptic exceptions.

After installing the SDK from the downloaded installer, you will have to start the `SDK Manager` as an Administrator (Search on Start for SDK Manager, right click the SDK Manager icon and select `Run as Administrator`) and install the following:

1. Tools
	1. Android SDK Tools
	2. Android SDK Platform-tools
	3. Android SDK Build-tools
2. Android 6.0 (API 23)
	1. Documentation
	2. SDK Platform
	3. Samples for SDK (Optional)
	4. Google APIs
3. Android 5.1.1 (API 22) (Only because my device is 5.1.1, you can avoid it. 6.0 might even be BC so this might be a waste of time)
	1. Documentation
	2. SDK Platform
	3. Samples for SDK (Optional)
	4. Google APIs
4. Extras
	1. Local Maven repository for Support Libraries
	2. Android Support Library
	3. Google Play services
	4. Google Repository
	5. Google Play APK Expansion Library
	6. Google USB Driver

Do you need all of these? Probably not. Do you need any of these? Yes. Which ones? God knows - I've managed to get everything working with all these installed and I'm going to stick with it. Some things, like the Documentation and Google APIs, are not necessary just to build an Android app. Google APIs are used to make some Google specific services available to your app, but your app might not need them at all.

<h2>3. Installing required plugins</h2>

The worst is nearly done. In the process of wanting to pull my hair out after creating multiple projects with the provided templates for Android and Scala, I finally went on Freenode and asked the #scala channel in large if anyone had done this before.

Surprisingly, the creator of the `android-sdk-plugin`, pfn, was around. He told me to go to my `~/.sbt/0.13/plugins/` directory and run the following command:

```
echo addSbtPlugin("com.hanhuy.sbt" % "android-sdk-plugin" % "1.5.18") > android.sbt
```

The `~` is your user profile directory, usually `C:\Users\<user>\` directory. Create the necessary directories to form the path `C:\Users\<user>\.sbt\0.13\plugins` (Replace the <user> with your username). Open a command prompt in that location and run the command above. This will make the `Android SDK plugin` accessible through `SBT`.

That is all! Now you're set to create an Android app in Scala!

<h2>4. Creating your first app</h2>

<h3>Using Activator:</h3>

Now that all the ground work is done, let's create our first app. In `IntelliJ`, select `File > New > Project`. Now select `Scala` in the left pane, and `Activator` in the right pane. Give a name to your project, and select the `Scala on Android - Preparing the environment` template. Let IntelliJ download all the plugins required for the project (about half the Internet) and set yor project up. This may take a few (a lot of) minutes so make yourself some coffee, read a book or something else (get married) to fill the time.

Once that's done, you should be able to start the app by clicking on the `SampleActivity` run configuration, or by launching `SBT` in the project directory and typing in `android:run`.

<h3>Using the command prompt:</h3>

You can also create an app from the command line using:

    sbt "gen-android android-23 com.foo Foo"

But this will generate an SBT-backed Java Android application. You can run this by opening the SBT shell and typing `android:run`. Import the project into IntelliJ and convert the Java code to Scala as appropriate. (This is the advice given to me by people who've probably done this before. But I couldn't get it to work for me as easily. I stuck to the Activator Template method above.)

<h2>5. Troubleshooting</h2>

Now let's stop dreaming about things working smoothly and come to the real world of pain. There are several things that don't work properly with the above mentioned scenario.

Some of the exceptions are below:

1. ProGuard issues:

When you compile your project, it fails with this exception:

    (android:proguard) proguard.ParseException: Expecting class path separator ';' before '\Android\android-sdk\platforms\android-23\android.jar' in argument number 121

This occurs if you didn't listen to what I said above about not having spaces in your Android SDK installation location. To solve this, move your SDK to a path without spaces and set your `ANDROID_HOME` environment variable correctly.

2. Mysterious Resolver Indexer errors

When you compile this project, you can see this message:

    Resolver Indexer: Repository is absent or invalid: C:\Program%20Files%20(x86)\Android\android-sdk\extras\android\m2repository
    
This too probably occurs because of an installation location with spaces (As you can see above). I haven't had issues because of this, and I didn't do anything to fix this in particular. Once I fixed the problem above, the project started working so I didn't worry about this.

3. Class already found - BUT THERE IS ONLY ONE!

When you try to compile your project, you get this error:

    SampleActivity is already defined as class SampleActivity class SampleActivity extends Activity

This one really annoyed me. I had just created a new project out of a template and it was giving me this error. Be brave, this error is not an error. It's not able to compile things properly and there are some other issues in your setup. Maybe your SDK isn't in the right location. Maybe your SBT isn't setup correctly. Whatever the case may be, this is not to be fixed directly in any case. At least not in the example given above. If you encounter this, your problem lies elsewhere.

Try to create a new Android project using just SBT and see if that helps. You can create this by running:

    sbt "gen-android android-23 com.foo Foo"

This will create a new project right in the location the command was run. Now launch SBT there using `sbt` and run your project using `android:run`. This will download a bunch of dependencies if any were missing and should start the project for you.

<h2>6. Conclusion</h2>

You must *really* want to develop things in Scala if you took this route. "It works, BUT AT WHAT COST!?" I hear you shout. That's a journey I haven't completed yet. If you think you're great at Scala, then this is probably a great option for you. The cost is entirely dependent on you.

Hope you manage to get this to work for you! I'm off to write something simple. If you think you have lots of time and would like to help me out a little, please feel free to send me an email: admin @ caffinc . com.
 