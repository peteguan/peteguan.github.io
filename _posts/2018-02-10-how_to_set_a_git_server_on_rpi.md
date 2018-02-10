---
layout: post
title:  "How to Set a Private Git Server on Raspberry Pi"
date:   2018-02-07
excerpt: "Git is a fantastic version control system in many reasons. Sometimes I think if the Raspberry Pi could be used as a git server in my home, all my
Git repos will have their own private server. Finally I made it."
image: "/images/20180210173838.jpg"
---

## What you need


Of course a Raspberry Pi. Mine is a Raspberry Pi 3 model B and this tiny server works fine. If you want to save your cost just choose an older version but you have to make a blance between spending and performance.  
A TF card is necessary for a Raspberry Pi (at least you need to install OS on the TF card.) The storage depends on how large are your repositories.  
That's all for me. Maybe you need a monitor. How I control the Pi is to use an SSH client such as PuTTy, secureCRT or Xshell. [PuTTy](https://www.putty.org/) is a free, open source one.  
I will write another article about how to start SSH service for a Pi for the first time using after installing operate system without monitor.  

## Add a group and a user for git

Let's create a new group and new user for git, and name it "git".
```
adduser --system --shell /bin/bash --gecos 'git version control by pi' --group --home /home/git git        
passwd git     
su git

```
