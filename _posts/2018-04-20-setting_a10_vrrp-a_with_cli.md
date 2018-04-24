---
layout: post
title:  "Setting A10 VRRP-A High Availability & aVCS with CLI"
date:   2018-04-20
excerpt: "This is an experiment of A10 devices VRRP-A High Availability and aVCS configuration."
image: "/images/A10-Logo.jpg"
---

## Purpose


* Deploy a pair of AX 3530 devices as <i>Active-Standby</i> VRRP-A HA, which means One ACOS device is the primary SLB device for all virtual services on which HA is enabled. The other ACOS device is a hot Standby for the virtual services.

* Upgrade system separately.

## Experiment Materials

* AX 3530 with system version 2.7.1-GR(build:58) x 2

* Laptop

* Finisar 1G Fibre Channel Optical Transceiver x 4

* Console cable x 1

* Fibre cable x 2

* Ethernet cable x 1

## Experiment Process

First, we connected A10 devices to laptop via a console cable plugged into the console port to check system version and deploy basic configurations like timezone.
```
clock timezone Asia/Shanghai
```

We named these two devices <i>ADC005</i> and <i>ADC006</i> with configurating management IP addresses 192.168.0.5 and 192.168.0.6 in configuration mode.

```
hostname ADC005
interface ethernet mgmt-
```

```
interface ethernet 5
lacp trunk 1 mode actice
```
I named the folder with my name. Change it to whatever you want.  

## Push your code to the Pi

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
ref: http://shumeipai.nxez.com/2014/02/15/git-build-private-server-raspberry-pi.html
