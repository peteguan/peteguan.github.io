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
interface ethernet management
ip address 192.168.0.5 /24
ip default-gateway 192.168.0.1
```
We uses the similar commands in <i>ADC006</i>,
```
hostname ADC006
interface ethernet management
ip address 192.168.0.6 /24
ip default-gateway 192.168.0.1
```

```
interface ethernet 5
lacp trunk 1 mode actice
```
I named the folder with my name. Change it to whatever you want.  
## Upgrade System Version



## Experiment Tips

* Remember always save memory and secondary memory when setting configurations.
```
write memory
write memory secondary
```
* Remember always check settings by <i>show</i> commands.
```
show running-config
show interfaces brief
show
```

## Test it

Try to clone the repository on your PC with Git Bush terminal with a line command   
```
git clone git@[IP of the Pi]:/home/git/pete.git
```
<br><br><br><br><br><br><br><br><br>
ref: http://shumeipai.nxez.com/2014/02/15/git-build-private-server-raspberry-pi.html
