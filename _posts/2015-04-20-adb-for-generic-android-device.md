---
title: ADB for generic Android device
author: admin
layout: post
permalink: /2015/04/adb-for-generic-android-device/
videourl:
  - 
categories:
  - Android Development
---
While working on an Android project several months ago, I found that my cheap generic Chinese Android tablet wouldn&#8217;t get detected by ADB. This made it difficult to debug the applications on the device. After Googling for several hours, I decided to dive into the matter myself, and came up with this solution, which I&#8217;ve successfully applied to several generic devices after that.

I was on a Windows 8 machine, but this should work for most Windows or Linux machines. The idea remains the same.

I went to my device manager and checked the properties of the device labeled &#8220;Android&#8221; which had the exclamation mark next to it.

Under the &#8220;Details&#8221; tab, I checked the hardware ID and found:

USB\VID\_18D1&#038;PID\_4E22&#038;REV\_0231&#038;MI\_01  
USB\VID\_18D1&#038;PID\_4E22&#038;MI_01

I decided to Google this and see if there are other devices that have the same Hardware ID so I could install that driver and check if it works. Miraculously, it took me to a page on XDA Developers where there was someone who got their device (A Nexus S) to work by changing the &#8220;android\_winusb.inf&#8221; file located in the &#8220;android-sdks\extras\google\usb\_driver&#8221; folder.

I checked for the file on my system, and thought I&#8217;d add a line to it. However, before doing that I thought &#8220;Well, let me just install these drivers before I go ahead and add a new device.&#8221; I right-clicked on the file and chose &#8220;Install&#8221;.

Ta-da! My device got detected as some random Samsung device, and in the device manager, now I could see a shiny blue &#8220;Android Device&#8221; with an &#8220;Android Composite ADB Interface&#8221; in it. I checked ADB and there it was! My tablet was being detected. I tested a few shell scripts on it, and it works just fine 😀

Long story short, and for people who go &#8220;TL;DR&#8221;, here&#8217;s what you have to do to get your tablet working with ADB:  
1. Enable USB debugging  
2. Go to your &#8220;android-sdks\extras\google\usb\_driver&#8221; folder and right click on &#8220;android\_winusb.inf&#8221; file and choose &#8220;Install&#8221;  
3. Your device would get installed as a generic device and ADB should work now!

Hope this saves someone a LOT of time, I know I&#8217;d have saved some if I had found this.