---
layout: post
title:  "How to Set a Private Git Server on Raspberry Pi"
date:   2018-02-10
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

Let's create a new group and new user for git, and name it "git", set the password "git" as well.
```
adduser --system --shell /bin/bash --gecos 'git version control by pi' --group --home /home/git git        
passwd git     
su git
```

## Add a new git repository

Let's come to the path you just created,  
```
cd /home/git
```
We need to create a new folder for the repository, and make it a real repository folder.  
```
mkdir pete.git
cd pete.git
git --bare init
```
I named the folder with my name. Change it to whatever you want.  

## Push your code on the Pi

You need to know the IP of your Pi. If you are using a monitor, simply input <i>ifconfig</i> in the terminal as a normal Linux computer. Or scan the Pi's IP with the tool [Advanced IP Scanner](http://www.advanced-ip-scanner.com/).

Then you could add a new git remote with the command below
```
git remote add pi git@[IP of the Pi]:/home/git/pete.git
```
Now you can add your codes in this folder, then push them to the Pi
```
git add .
git commit -m "initial commit"
git push pi master
```

## Test it

Try to clone the repository on your PC with Git Bush terminal with a line command   
```
git clone git@[IP of the Pi]:/home/git/pete.git
```
<br><br><br><br><br><br><br><br><br>
ref:http://shumeipai.nxez.com/2014/02/15/git-build-private-server-raspberry-pi.html
