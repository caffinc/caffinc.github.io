---
title: Redirect a Port or Socket
author: admin
layout: post
permalink: /2015/11/redirect-port/
categories:
  - Java
tags:
  - web
  - sockets
  - ports
  - redirect
---

Before I started using VPNs, I wanted a way to connect to an IRC server running at `irc.rizon.net:6667` from any of my devices, without compromising my IP address. I decided that I'd make my own proxy server, but only one port at a time.

Conceptually it seemed straight forward to do it - so I wrote this project which redirects one port on the proxy machine to a port on the target machine. I admit that when it actually worked, I was more surprised than I'd like to admit!

The idea was simple - write a "server" that waits on the proxy machine `P` for a connection on port `X` from client `C`, and redirects that to a new connection opened to the target machine `T` on port `Y`. Failure in the connection `C <-X-> P` disconnects `P <-Y-> T`, and vice-versa.

    // Blocks the main thread on proxy server P while waiting for a connection
    Socket clientConnection = server.accept();
    System.out.println( "Got a connection from " + clientConnection.getInetAddress() );
    
    // Open streams to the client C
    InputStream serverIn = clientConnection.getInputStream();
    OutputStream serverOut = clientConnection.getOutputStream();

    // Connect to the target T and start redirection
    Socket targetCon = new Socket( host, outputPort );
    InputStream targetIn = targetCon.getInputStream();
    OutputStream targetOut = targetCon.getOutputStream();

Since I wanted to be able to redirect multiple connections at once, I had each of them running in their own thread. I open the streams and hand it off to a different thread to do the streaming in and out:

    RedirectThread outgoing = new RedirectThread( targetIn, serverOut );
    RedirectThread incoming = new RedirectThread( serverIn, targetOut );

My thread which does the data redirection looks something like this:

    class RedirectThread extends Thread
    {
        InputStream in;
        OutputStream out;


        public RedirectThread( InputStream in, OutputStream out )
        {
            this.in = in;
            this.out = out;
        }


        @Override
        public void run()
        {
            byte[] buffer = new byte[1024];
            int bytesRead;
            try {
                for ( ;; ) {
                    bytesRead = in.read( buffer );
                    if ( bytesRead == -1 ) {
                        out.close();
                        return;
                    }
                    out.write( buffer, 0, bytesRead );
                }
            } catch ( Exception e ) {
                e.printStackTrace();
            }
        }
    }

It took under an hour to write this and it worked! I tossed it on an Amazon instance I had access to, and connected my IRC client to it and it worked like magic! I was connected to the IRC server without compromising on my own IP. Ofcourse this does not prevent your client from doing something boneheaded like hand over your IP if someone on the server sent a request for it (Like mibbit's web IRC client).

You can find all the code in this [Github repository here](https://github.com/caffinc/SocketRedirect "SocketRedirect") so you can set it up yourself. You can compile the whole thing into an executable jar file that's just a few KBs in size using `mvn clean package`, or grab a pre-compiled jar [here](https://github.com/caffinc/SocketRedirect/releases/download/v1.0/socketredirect-1.0-SNAPSHOT.jar "Pre-compiled executable jar"). 

If you do end up using it, please drop me an [email](mailto:admin@caffinc.com)!