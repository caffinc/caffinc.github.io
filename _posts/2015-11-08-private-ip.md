---
title: How to check if IP is private?
author: admin
layout: post
permalink: /2015/11/check-private-ip/
categories:
  - Java
tags:
  - web
  - server
  - ip
---

While designing systems that auto-scale, one of the roadblocks that I hit was being unable to identify the public IP of the machine I was running my code on.

The following code snippet adapted from [here](http://stackoverflow.com/questions/9481865/getting-the-ip-address-of-the-current-machine-using-java "IP address of current machine") is a very good solution. Note however that this *may* not be foolproof, so use this at your own discretion:

    public static boolean isPrivateIP( String ip )
    {
        boolean local = ip.startsWith( "192.168." ) || ip.startsWith( "10." );
        if ( local ) {
            return true;
        } else {
            if ( ip.startsWith( "172." ) ) {
                Integer second = Integer.parseInt( ip.split( "\\." )[1] );
                if ( second >= 16 && second <= 31 ) {
                    return true;
                }
            }
        }
        return false;
    }

Software defined private networks like `Docker's` networks, or `GCE` private networks start with `10.` and `LAN` usually starts with `192.168.`. `Amazon's` internal IPs usually start with `172.` followed by a number between 16 and 31 (inclusive). These might be industry standards for IPs defined in some protocol in fineprint, but I'm not the authority. [This might be a starting point.](https://en.wikipedia.org/wiki/IP_address)

I'll discuss how to use this information to find your public IP in the next post.