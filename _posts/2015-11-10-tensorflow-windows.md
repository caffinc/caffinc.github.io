---
title: TensorFlow on Windows
author: admin
layout: post
permalink: /2015/11/tensorflow-windows/
comments: true
categories:
  - TensorFlow
  - Windows
tags:
  - windows
  - tensorflow
  - google
  - machine learning
  - big data
  - neural networks
  - deep learning
  - docker
---
![TensorFlow Logo](http://tensorflow.org/images/logo-alt@2x.png)

---
edit (16 Feb 2017):

Google updated their URLs and TensorFlow's matured a little more. These are the latest instructions:

1. Get [Python 3.5 x64](https://www.python.org/downloads/release/python-353/)
2. Install TensorFlow using (one of):

    ```pip3 install --upgrade tensorflow```

    ```pip3 install --upgrade tensorflow-gpu```

You can find this info on the official [TensorFlow Windows Installation page](https://www.tensorflow.org/install/install_windows).

edit (27 Dec 2016):

Over a year later, I still see a few people looking at this post and it makes me feel good. Tensorflow now has native support on Windows so you should use that instead!

You can get it using:

    pip install --upgrade https://storage.googleapis.com/tensorflow/windows/gpu/tensorflow_gpu-0.12.0rc0-cp35-cp35m-win_amd64.whl

Official instructions can be found on the [TensorFlow website](https://www.tensorflow.org/get_started/os_setup#pip_installation).

Also, my VPS which hosted the images that were on this page decided that it was a good idea to reset it without consulting with me. I lost a lot of my data and images in the process, which mean that some of the screenshots that were on this page no longer exist. I'm sorry about that.

---
## Original post:


`TensorFlow` is the new machine learning library released by Google. I'm quite excited about it and can't wait to try it out.

However, like most open-source software lately, it's not straight-forward to get it to work with Windows. I have a Windows 10 machine and enjoy coding on it immensely. Linux isn't quite for me at this moment, maybe in a year or two.

So coming straight to the point - I looked through the installation instructions for `TensorFlow` on their [website](http://tensorflow.org/get_started/os_setup.md) and as expected I didn't find Windows there. I did find `Docker` instructions:

>First, install Docker. Once Docker is up and running, you can start a container with one command:
>
>`$ docker run -it b.gcr.io/tensorflow/tensorflow`
>
>This will start a container with TensorFlow and all its dependencies already installed.

However things didn't work quite the way described here. I installed the latest `Docker Toolbox` from their [website](https://www.docker.com/docker-toolbox "Docker website") (v1.9.0b) and started it up. I got the Docker Quickstart Terminal screen:

![Docker Quickstart Terminal](http://caffinc.com/wp-content/uploads/2015/11/docker-quickstart-terminal.png)

If you have issues installing Docker, you might want to [check my other post](http://caffinc.github.io/2015/09/docker-getting-started/).

I typed in the command as shown and got this:

    $ docker run -it b.gcr.io/tensorflow/tensorflow
    cannot enable tty mode on non tty input

Well, that's great. I searched for a solution and found that there was a bug with `MinGW` and I had to append a `winpty` to get this to work on Windows (It was right there in the little note shown in the terminal... if only I read instructions...)

    $ winpty docker run -it b.gcr.io/tensorflow/tensorflow
    Unable to find image 'b.gcr.io\tensorflow\tensorflow:latest' locally
    repository name component must match "[a-z0-9](?:-*[a-z0-9])*(?:[._][a-z0-9](?:-*[a-z0-9])*)*"

*Come on...* This made no sense. Why was it trying to find it locally? And why wouldn't it download from a remote location? The repository name was from Google and it was obviously tested! (Right?) I opened up the repository URL (b.gcr.io/tensorflow/tensorflow) in my browser and it gave me a `404`.

I thought "Well, let me just `pull` the repository to see if that works..." and it did!

    $ docker pull b.gcr.io/tensorflow/tensorflow

That downloaded the `TensorFlow` image to my machine. I checked it using the `docker images` command:

    REPOSITORY                       TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    b.gcr.io/tensorflow/tensorflow   latest              217daf2537d2        3 days ago          652.6 MB

Now I tried starting it again using `winpty docker run -it b.gcr.io/tensorflow/tensorflow`

    Unable to find image 'b.gcr.io\tensorflow\tensorflow:latest' locally
    repository name component must match "[a-z0-9](?:-*[a-z0-9])*(?:[._][a-z0-9](?:-*[a-z0-9])*)*"

Close but still no cigar... I decided to use the `IMAGE ID` instead (In this case, `217daf2537d2`):

    $ winpty docker run -it 217daf2537d2
    root@881b2215b26b:/#

w00t w00t! It seems to have worked! Now to start playing around with `TensorFlow`! Hope this helps people on Windows and Docker who can't get it to work as described!

---
*edit (12 Nov 2015):*

I got a few emails from people asking me if I managed to get things to work. The answer is Yes. You need to use the Docker Quickstart Terminal and start the TensorFlow image as described above. Once you're in the TensorFlow shell, you can type `python` to start Python on it, and run your Python code inside it.

I've tried out a few samples from the [TensorFlow Basic Usage page](http://tensorflow.org/get_started/basic_usage.md) so far, and they all seem to work. Here's a screenshot:

![Docker running TensorFlow](http://caffinc.com/wp-content/uploads/2015/11/docker-tensorflow.png)

Good luck with your experimentation!
