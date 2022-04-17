---
title: "Corrupt Disk: Diskpark or Disk Management will not start on Windows"
author: admin
layout: post
permalink: /2016/12/corrupt-disk-fix/
categories:
  - Diskpart
  - Disk Management
  - Windows
  - GParted
tags:
  - gparted
  - diskpart
  - windows
---

I recently purchased a Raspberry Pi 3 and wanted to play around with it. After flashing the Raspbian OS onto the MicroSD card using Win32DiskImager, I noticed that it had created two partitions - a 63MiB FAT32 one called `BOOT` and another hidden ext4 one which contained the actual OS.

I wanted to clean up the MicroSD card after playing around, and since Windows does not play nice with ext4, [I searched online and found](https://www.all4os.com/windows/properly-delete-a-partition-on-usb-drive-using-diskpart.html) that the `Diskpart` utility on Windows works well for cleaning up these partitions and restoring my whole card. Yay! That worked as planned and I reinstalled the OS onto the MicroSD card for another run.

On my third attempt however I was a bit careless with using diskpart, and called the `delete partition` command on the ext4 partition instead of just calling the `clean` command on the disk. The window just froze for a while. After waiting for a few minutes, my patience ran out. I killed the diskpart process and tried starting again. Well... it wouldn't start.

![Frozen Diskpart](/assets/images/diskpart_frozen.png)

It was frozen on the "On computer: ..." part, and restarting the computer wouldn't fix it. None of the other partitioning tools I had on my machine worked either. Disk Management wouldn't start either, being frozen on `Connecting to Virtual Disk Service`

![Frozen Disk Management](/assets/images/diskmanagement_frozen.png)

Explorer wouldn't treat the disk correctly, even after unplugging and plugging it again, or restarting the machine.

I searched on the Internet and a rather [unhelpful alarmist solution showed up](http://superuser.com/questions/819056/diskpart-will-not-open-through-cmd). Fearing the worst, I used my bootable USB PuppyLinux and got into Linux. From there I opened up GParted. It treated the disk like there was nothing wrong with it. Well, that's a good start. I clicked on the partition and deleted it. It seemed to be going alright. I created a new FAT32 partition on the whole disk and everything worked just as planned.

So there you have it! If Diskpart or Disk Management doesn't want to touch your SD Card and seems to have issues with it, a health dose of GParted might fix things for you!

Hope this helps someone before they decide to toss out their SD card! Happy hacking!  