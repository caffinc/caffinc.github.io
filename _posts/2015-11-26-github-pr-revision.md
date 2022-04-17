---
title: "ERROR: Couldn't find any revision to build"
author: admin
layout: post
permalink: /2015/11/github-pr-revision/
comments: true
categories:
  - GitHub
  - Jenkins
  - Java
tags:
  - jenkins
  - github
  - pr
  - github pr builder
  - revision
---

I love the [GitHub Pull Request Builder](https://plugins.jenkins.io/ghprb/)! It makes life so much easier by ensuring that pull requests are tested before they are merged into the main code. I have this running on most of my projects now, and I can't stress enough how useful this tool is. It adds a little ✓ or ❌ next to the PR depending on whether the build succeeded or failed.

Recently, while working on migrating the Jenkins CI on a GCE instance to an Amazon EC2 instance, I was faced with a weird problem. I had installed the GitHub PR Builder on the new Jenkins instance, and it would fetch PRs to build, but most of them (Not all) would fail with the following log:

    GitHub pull request #598 of commit cb800c68aa4ee5a94c8d1d009cd7805, no merge conflicts.
    Setting status of cb800c68aa4ee5a94c8d1d009cd7805 to PENDING with url http://<domain>/jenkins/job/PR%20Builder/14/ and message: 'Build started sha1 is merged.'
    Building in workspace /var/lib/jenkins/jobs/PR Builder/workspace
     > git rev-parse --is-inside-work-tree # timeout=10
    Fetching changes from the remote Git repository
     > git config remote.origin.url https://github.com/<repo-owner>/<repo> # timeout=10
    Fetching upstream changes from https://github.com/<repo-owner>/<repo>
     > git --version # timeout=10
    using .gitcredentials to set credentials
     > git config --local credential.username jenkins-bot # timeout=10
     > git config --local credential.helper store --file=/tmp/git218585661055512.credentials # timeout=10
     > git -c core.askpass=true fetch --tags --progress https://github.com/<repo-owner>/<repo> +refs/heads/*:refs/remotes/origin/*
     > git config --local --remove-section credential # timeout=10
     > git rev-parse refs/remotes/origin/pr/598/merge^{commit} # timeout=10
     > git rev-parse refs/remotes/origin/origin/pr/598/merge^{commit} # timeout=10
     > git rev-parse origin/pr/598/merge^{commit} # timeout=10
    ERROR: Couldn't find any revision to build. Verify the repository and branch configuration for this job.
    Setting status of cb800c68aa4ee5a94c8d1d009cd7805 to FAILURE with url http://<domain>/jenkins/job/PR%20Builder/14/ and message: 'Build finished. No test results found.'
    Finished: FAILURE

After searching around a bit, I couldn't figure it out. I had set everything up correctly, and yet I kept getting this message even after coaxing the PR Builder to test repeatedly.

As a last resort, I started looking through the project configuration more carefully. I noticed there was an `Advanced` button:
![Git PR Builder Advanced Button (Closed)](/assets/images/2015/11/git-pr-adv-closed.png)

I clicked it and noticed that I'd missed setting the `Refspec` field:
![Git PR Builder Advanced Button (Expanded)](/assets/images/2015/11/git-pr-adv-expanded.png)

I put in `+refs/pull/*:refs/remotes/origin/pr/*` and issued another request via GitHub and this time everything worked correctly! (Well... not everything. I had test failures, but that was a different problem altogether ;))

Hope this helps someone else in need!