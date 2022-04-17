---
title: ADB for generic Android device
author: admin
layout: post
permalink: /2015/04/adb-for-generic-android-device/
categories:
  - Android Development
---
While working on an Android project several months ago, I found that my cheap generic Chinese Android tablet wouldn't get detected by ADB. This made it difficult to debug the applications on the device. After Googling for several hours, I decided to dive into the matter myself, and came up with this solution, which I've successfully applied to several generic devices after that.

I was on a Windows 8 machine, but this should work for most Windows or Linux machines. The idea remains the same.

I went to my device manager and checked the properties of the device labeled `Android` which had the exclamation mark next to it.

Under the `Details` tab, I checked the hardware ID and found:

```
USB\VID\_18D1&#038;PID\_4E22&#038;REV\_0231&#038;MI\_01  
USB\VID\_18D1&#038;PID\_4E22&#038;MI_01
```

I decided to Google this and see if there are other devices that have the same Hardware ID so I could install that driver and check if it works. Miraculously, it took me to a page on XDA Developers where there was someone who got their device (A Nexus S) to work by changing the `android/_winusb.inf` file located in the `android-sdks/extras/google/usb/_driver` folder.

I checked for the file on my system, and thought I'd add a line to it. However, before doing that I thought 'Well, let me just install these drivers before I go ahead and add a new device.' I right-clicked on the file and chose `Install`.

Ta-da! My device got detected as some random Samsung device, and in the device manager, now I could see a shiny blue `Android Device` with an `Android Composite ADB Interface` in it. I checked ADB and there it was! My tablet was being detected. I tested a few shell scripts on it, and it works just fine 😀

Long story short, and for people who go 'TL;DR', here's what you have to do to get your tablet working with ADB:
1. Enable USB debugging
2. Go to your `android-sdks/extras/google/usb/_driver` folder and right click on `android/_winusb.inf` file and choose `Install`
3. Your device would get installed as a generic device and ADB should work now!

Hope this saves someone a LOT of time, I know I'd have saved some if I had found this.
