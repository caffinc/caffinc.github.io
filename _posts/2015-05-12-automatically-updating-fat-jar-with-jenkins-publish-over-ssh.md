---
title: Automatically updating fat jar with Jenkins Publish over SSH
author: admin
layout: post
permalink: /2015/05/automatically-updating-fat-jar-with-jenkins-publish-over-ssh/
videourl:
  - 
shareaholic_disable_recommendations:
  - 1
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
[<img class="aligncenter size-full wp-image-59" src="http://i1.wp.com/caffinc.com/blog/wp-content/uploads/2015/05/post_build_send_artifacts_over_ssh.png?fit=788%2C446" alt="post_build_send_artifacts_over_ssh" data-recalc-dims="1" />][1]

In bash, you can access the PID of the last executed command using `$!`  
We add transfer sets to execute commands to kill the previously running process, copy the jar, and start a new process. However, this is not as straightforward as you&#8217;d think. I ran this script to run the jar and store the pid in a file called `xyz.pid`:

> `#!/bin/bash<br />
nohup /usr/bin/java -jar /var/lib/xyz/xyz.jar &<br />
echo $! >> /var/lib/xyz/xyz.pid`

The first line is just to indicate that this is a bash script, I&#8217;m not sure if that&#8217;s required. The second line starts the jar in the background using [nohup][2]. The third line pipes the PID into a file. So far so good.

Now that the PID is getting written to a file, I add a transfer set **before** this one to stop the running process using the PID from the file, and delete the xyz.pid file:

> `#!/bin/bash<br />
FILE=/var/lib/xyz/xyz.pid<br />
if [ -f "$FILE" ];<br />
then<br />
read pid < "$FILE"<br />
kill "$pid"<br />
rm "$FILE"<br />
fi`

My final configuration so far looks something like this:  
[<img class="aligncenter size-full wp-image-64" src="http://i1.wp.com/caffinc.com/blog/wp-content/uploads/2015/05/build_transfer_sets.png?fit=788%2C781" alt="build_transfer_sets" data-recalc-dims="1" />][3]

This *should* work. But it doesn&#8217;t. The running process (if there is one, and the pid file is present) is killed. The new process starts as expected. But the build freezes and after a couple of minutes, gets set to **Unstable**. If you look at the Console Output in Jenkins, you can see that it is waiting for the script to return after starting the jar file and piping the pid into a file. After trying all combinations of scripts, using exec, start, with &, without &, I was nearly at my wit&#8217;s end. I then discovered that in the **Advanced&#8230;** options there was a **Exec in pty** checkbox. I checked this, and my builds were no longer timing out (Set in the **Exec timeout (ms)**) and the build was succeeding. But now the jar was no longer running! <img src="http://i1.wp.com/caffinc.com/blog/wp-includes/images/smilies/frownie.png?w=788" alt=":(" class="wp-smiley" style="height: 1em; max-height: 1em;" data-recalc-dims="1" />

I tried several more combinations of scripts, and eventually I realized that the jar was getting executed, but was getting killed before it actually ran. I discovered this after adding a `sleep 60` in the execution script. And suddenly, the jar *stayed* running, even when the build completed!

I now changed the exec script to this:

> `#!/bin/bash<br />
nohup /usr/bin/java -jar /var/lib/xyz/xyz.jar &#038;<br />
echo $! >> /var/lib/xyz/xyz.pid<br />
sleep 1`

I assume the issue was with the nohup being unable to change the parent of the command to root soon enough, and so the process got killed as soon as the SSH was disconnected. I left the **Exec in pty** checkbox checked. This build now works as required! Every time I execute the build, the old process is killed, a new jar takes its place and it&#8217;s started, all in a matter of a few seconds!

 [1]: http://i1.wp.com/caffinc.com/blog/wp-content/uploads/2015/05/post_build_send_artifacts_over_ssh.png
 [2]: http://www.cyberciti.biz/tips/nohup-execute-commands-after-you-exit-from-a-shell-prompt.html
 [3]: http://i1.wp.com/caffinc.com/blog/wp-content/uploads/2015/05/build_transfer_sets.png