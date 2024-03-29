---
title: Automatically updating fat jar with Jenkins Publish over SSH
author: admin
layout: post
permalink: /2015/05/automatically-updating-fat-jar-with-jenkins-publish-over-ssh/
categories:
  - Java
  - Jenkins
tags:
  - execute jar
  - fat jar
  - jenkins
  - publish
  - publish over ssh
  - ssh
---
While using the <a href="https://wiki.jenkins-ci.org/display/JENKINS/Publish+Over+SSH+Plugin" target="_blank">Publish over SSH plugin for Jenkins</a> in a project recently, I thought it would be great to have Jenkins restart the deployed <a href="https://maven.apache.org/plugins/maven-shade-plugin/" target="_blank">fat jar</a> on the remote server.  
My jar was an embedded jetty project, which was a neatly self-contained web project. My goal was the bring the running process down, transfer the fat jar, and start it again.  
Deploying the jar using the plugin was straightforward. I configured the project to send build artifacts over SSH:  
![post_build_send_artifacts_over_ssh](/assets/images/2015/05/post_build_send_artifacts_over_ssh.png)

In bash, you can access the PID of the last executed command using `$!`  
We add transfer sets to execute commands to kill the previously running process, copy the jar, and start a new process. However, this is not as straightforward as you'd think. I ran this script to run the jar and store the pid in a file called `xyz.pid`:

    #!/bin/bash
    nohup /usr/bin/java -jar /var/lib/xyz/xyz.jar &
    echo $! >> /var/lib/xyz/xyz.pid

The first line is just to indicate that this is a bash script, I'm not sure if that's required. The second line starts the jar in the background using [nohup][2]. The third line pipes the PID into a file. So far so good.

Now that the PID is getting written to a file, I add a transfer set **before** this one to stop the running process using the PID from the file, and delete the xyz.pid file:

    #!/bin/bash
    FILE=/var/lib/xyz/xyz.pid
    if [ -f "$FILE" ];
    then
    read pid < "$FILE"
    kill "$pid"
    rm "$FILE"
    fi

My final configuration so far looks something like this:  
![build_transfer_sets](/assets/images/2015/05/build_transfer_sets.png)

This *should* work. But it doesn't. The running process (if there is one, and the pid file is present) is killed. The new process starts as expected. But the build freezes and after a couple of minutes, gets set to **Unstable**. If you look at the Console Output in Jenkins, you can see that it is waiting for the script to return after starting the jar file and piping the pid into a file. After trying all combinations of scripts, using exec, start, with &, without &, I was nearly at my wit's end. I then discovered that in the **Advanced!** options there was a **Exec in pty** checkbox. I checked this, and my builds were no longer timing out (Set in the **Exec timeout (ms)**) and the build was succeeding. But now the jar was no longer running! :(

I tried several more combinations of scripts, and eventually I realized that the jar was getting executed, but was getting killed before it actually ran. I discovered this after adding a `sleep 60` in the execution script. And suddenly, the jar *stayed* running, even when the build completed!

I now changed the exec script to this:

    #!/bin/bash
    nohup /usr/bin/java -jar /var/lib/xyz/xyz.jar &
    echo $! >> /var/lib/xyz/xyz.pid
    sleep 1

I assume the issue was with the nohup being unable to change the parent of the command to root soon enough, and so the process got killed as soon as the SSH was disconnected. I left the **Exec in pty** checkbox checked. This build now works as required! Every time I execute the build, the old process is killed, a new jar takes its place and it's started, all in a matter of a few seconds!

 [1]: /assets/images/2015/05/post_build_send_artifacts_over_ssh.png
 [2]: http://www.cyberciti.biz/tips/nohup-execute-commands-after-you-exit-from-a-shell-prompt.html
 [3]: /assets/images/2015/05/build_transfer_sets.png
