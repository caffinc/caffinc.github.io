---
title: Bluetooth headset blues
author: admin
layout: post
permalink: /2013/10/bluetooth-headset-blues/
categories:
  - Uncategorized
---
After much deliberation, I finally purchased a pair of Nokia BH-505 headsets:

<div style="width: 559px" class="wp-caption alignnone">
  <img alt="Nokia BH-505 White" src="http://caffinc.com/wp-content/uploads/2013/10/nokia-bh-505.jpg" data-recalc-dims="1" />
  
  <p class="wp-caption-text">
    Nokia BH-505 White
  </p>
</div>

&nbsp;

They were probably the cheapest I could find on the internet because soon after I bought them, they were sold out on pretty much every site I could find. The next price that I saw them for was on Flipkart for about 4k INR.

I found them a tad uncomfortable (though I&#8217;d convinced myself that this was just because the headset was new and that in a few days either I, or the headset would give). I paired it with my laptop, a HP Probook 4440s running Windows 8 Pro. The laptop picked up the device as &#8220;Nokia BH-&#8221; without a model number, but with a &#8220;-&#8220;. Soon after it installed the driver however, the headset showed up as Nokia BH-505, but the image looked like a mono-phone headset. &#8220;Oh well,&#8221; thought I, &#8220;As long as it works&#8230;&#8221;

Played a song on my laptop &#8211; sure enough, mediocre quality audio played out of the headset &#8220;Oh well,&#8221; thought I, &#8220;As long as it plays it clearly and works decently&#8221;. Then I tried making a call on Skype with the headsets. And that&#8217;s where everything went downhill. The moment Windows asked the device to use its mic, it started playing back the mic to my ears. So I could hear myself, and the world, back into my ears. The static&#8230; the background noise&#8230; terrible. I tried turning off the device, starting it again &#8211; didn&#8217;t help. Then I had a great idea &#8211; unpair the device, pair it again. So I clicked the &#8220;Remove device&#8221; option in the Devices and Printers. After about ten minutes, the device was still there. I thought &#8220;oh, maybe I didn&#8217;t get it right&#8221;. Clicked &#8220;Remove device&#8221; again. Nothing. I thought it was something akin to printers never cancelling print jobs until you turn them off, so I turned off the headset. Didn&#8217;t help. Figured maybe a restart of my laptop would fix it. Restarted the laptop. It stood circling the weird Windows 8 progress bar for a while before suddenly restarting again. When Windows started up again, it told me that there was a problem with my PC and that it has recovered from it.

I went to the Devices and Printers &#8211; and the headset was still there. I thought &#8220;Ok, let me connect it again and see if the noise issue is fixed&#8221;. Wouldn&#8217;t connect. In fact, my Bluetooth wouldn&#8217;t start anymore. I went to the options in Windows 8, saw that the WiFi was turned on, but Bluetooth wouldn&#8217;t work anymore. Searched everywhere, found a solution that advised reinstalling drivers. At the same time in the Device Manager, I noticed that there was a weird error &#8220;Unknown USB Device (Port Reset Failed)&#8221; with a weird hardware number &#8220;*USB\VID\_0000&PID\_0001\6&27BCB2B&0&6*&#8220;. Didn&#8217;t seem like a real device. I knew something really weird was happening.

Downloaded about 180mb of drivers for the Bluetooth (Seriously? 180mb for a hardware device? What the hell is this?). The driver didn&#8217;t detect the device. Figured it&#8217;s the wrong model (HP&#8217;s driver website for this laptop model lists three different hardware manufacturer&#8217;s drivers. I downloaded the newest, and possibly the largest file there). Downloaded the next driver, about 80mb. No luck there either. Device Manager didn&#8217;t show Bluetooth anymore.

I searched some more. There was a post about deleting Ghost devices: http://www.mytechguide.org/12179/remove-hidden-devices-from-windows/#. Tried that too, removed a lot of devices. In fact, I removed the whole USB section and the whole HID section. Still no luck (Although it did disable my USB wireless mouse and removed the headset from the Devices and Printers, but now my touchpad is a bit flaky).

Finally I restarted the system and checked my BIOS. There was an option to disable Bluetooth hardware. I disabled and re-enabled it. Restarted the machine. The USB devices were back in Windows 8, along with the weird Unknown USB Device (Port Reset Failed) problem. I was at my wits&#8217; end. (At this point however, whenever I clicked on the Bluetooth option in the settings, it would tell me &#8220;Could not turn on&#8221;, instead of merely trying to turn on, spinning, and stopping)

Tried removing it again and restarting several times, but no luck. I finally restarted the machine, went to the BIOS, disabled Bluetooth, logged into Windows, removed all the devices, restarted the machine, went to BIOS, enabled Bluetooth and logged back into Windows.

And this time &#8211; there was no &#8220;Unknown USB Device&#8221;. I reinstalled drivers &#8211; voila! I connected my headset &#8211; worked! Everything worked right. And during the course of going through all this, I&#8217;d paired the headset with my phone which made me realize that I need to hit the &#8220;Play/Pause&#8221; button once to make it stop streaming the mic to my ears and start playing the correct audio. (Crappy Firmware?)

Everything seems to be working great right now, though I don&#8217;t really like these headsets which weren&#8217;t really that cheap. I should have probably stuck to a wired pair with better quality. I should also say &#8211; Nokia&#8217;s accessories website is really, really crappy. They don&#8217;t have proper documentation, they have 10 different sites showing different sets of hardware, some sites aren&#8217;t even aware of the existence of their own hardware, and some have wrong info about their own products.

Here&#8217;s one of the sites about this headset:

http://www.nokia.com/global/products/accessory/bh-505/
