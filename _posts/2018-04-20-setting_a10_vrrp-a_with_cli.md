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
Now we could use a USB port on laptop connect <i>ADC005</i> console port and ethernet port on laptop connect <i>ADC006</i> management port.<br>
<b></b><i></i>
The next step is to build VRRP-A High Availability.<br>
We trunked <b>ethernet 5</b> and <b>6</b> together as <b>vlan 99</b> dynamically and set both ethernet ports an active mode. A virtual ethernet port <b>ve 99</b> for setting VLAN IP address is nessesary as well.<br>
The same commands for both ADC devices.
```
interface ethernet 5
enable
lacp trunk 1 mode actice
interface ethernet 6
enable
lacp trunk 1 mode actice
vlan 99
tagged trunk 1
router-interface ve 99
```
Then we started to set VRRP-A.<br>
Before enabling the function VRRP-A, we set <b>set-id</b> and <b>device-id</b> for each A10 3530. We set the set as 1, <b>ADC005</b> as <b>device 1</b> and <b>ADC006</b> as device 2.<br>
It is time to enable VRRP-A.
```
vrrp-a set-id 1
vrrp-a device-id 1
vrrp-a enable
```
The VRRP-A HA status changed from <i>Standby</i> to <i>Active</i>.
```
ADC005(config)#
ADC005-Standby(config)#    
ADC005-Active(config)#
```
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
