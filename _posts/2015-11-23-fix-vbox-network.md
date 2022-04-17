---
title: Fixing VERR_INTNET_FLT_IF_NOT_FOUND problems
author: admin
layout: post
permalink: /2015/11/fix-vbox-network/
categories:
  - Docker
  - VirtualBox
tags:
  - docker
  - network
  - adapter
  - virtualbox
  - hostinterfacenetworking
  - windows
  - november
---
I recently installed the [November Windows 10 update](http://blogs.windows.com/windowsexperience/2015/11/12/first-major-update-for-windows-10-available-today/) on my computer, and while everything went smoothly, my `VirtualBox` went belly-up.

I *knew* there would be something wrong with VirtualBox even before I started it but when I started up the `Docker Quickstart Terminal` and it exited with an error, my fears were confirmed.

![Docker Start Fail](/assets/images/2015/11/docker-start-fail.png)

I opened up VirtualBox and tried starting the `default` image manually and this is what I was greeted with:

![VirtualBox Network Error](/assets/images/2015/11/vbox-network-error.png)

The error message was:

    Failed to open a session for the virtual machine default.
    
    Failed to open/create the internal network 'HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter #6' (VERR_INTNET_FLT_IF_NOT_FOUND).
    
    Failed to attach the network LUN (VERR_INTNET_FLT_IF_NOT_FOUND).
    
    Result Code: E_FAIL (0x80004005)
    Component: ConsoleWrap
    Interface: IConsole {872da645-4a9b-1727-bee2-5585105b9eed}
    
I tried doing a few things - restarting VirtualBox, restarting my machine, reinstalling VirtualBox, reinstalling Docker Toolbox... nothing seemed to work. Then I searched on the Internet for a solution and there were several questions from back in 2014, and they had vague answers which didn't work for me or didn't seem to be related to my issue. I finally discovered this [gem](http://stackoverflow.com/questions/33725779/failed-to-open-create-the-internal-network-vagrant-on-windows10) which helped me solve it. It wasn't the first answer, but the second by [JustMe](http://stackoverflow.com/users/1291538/justme) that helped.

I opened my `Control Panel > Network & Internet > Network Connections` and located my VirtualBox Host-Only Network adapter, called `VirtualBox Host-Only Network #6` on my machine. I right-clicked it and selected the `Properties`, and checked the un-checked `VirtualBox NDIS6 Bridged Networking Driver` and started VirtualBox again.

![Fix the VirtualBox Host-Only Network adapter](/assets/images/2015/11/adapter-fix.png)

This time the `default` image started up without errors! If your checkbox is checked already, you might have to uncheck and check again. It might be buggy.

Note: This isn't the complete solution. It just fixes the issue with the images not starting up in VirtualBox. It does not fix the issue that prevents the Docker QuickStart Terminal from connecting to the booted image. For now, I just removed the image and let the QuickStart Terminal re-create the image, which works but also means that I have to download my images again :(

Note 2:
During the migration of my blog several times in the past years, I didn't have a robust backup mechanism set up, because "eh, it's just a small blog" butt unfortunately I now have no backup of all the images that were originally posted in this article.
