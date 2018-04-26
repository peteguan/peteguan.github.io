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

### Basic Configuration

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
The same commands for both ADC devices. (the <b>ve 99</b>IP address should be different)
```
interface ethernet 5
enable
lacp trunk 1 mode actice
interface ethernet 6
enable
lacp trunk 1 mode actice
interface ve 99
ip address 1.1.1.1 /24
vlan 99
tagged trunk 1
router-interface ve 99
```
### VRRP-A HA

Then we started to set VRRP-A.<br>
Before enabling the function VRRP-A, <b>set-id</b> and <b>device-id</b> for each A10 3530 should be claimed. We set the set as 1, <b>ADC005</b> as <b>device 1</b> and <b>ADC006</b> as device 2.<br>
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
For VRRP-A setting, we created a <i>failover-non-preemption</i> template. As the name saying, this template is for disable preempt when fail over.<br>
The following commands are for <b>ADC005</b>. For <b>ADC006</b>，the commands are exactly the same except for setting <i>priority</i> as <i>50</i>.
```
vrrp-a fail-over-policy-template failover-non-preemption
   trunk 1 weight 80
!
vrrp-a vrid default
   preempt-mode disable
   floating-ip 1.1.1.3
   priority 100
   fail-over-policy-template failover-non-preemption
!
```
Now we could find that the VRRP-A status of these two devices had became <i>Active</i> and <i>Standby</i>.
### VCS

We used the virtual interface <b>ve 99</b> as VCS transmit port. <b>ADC005</b> was set as <i>device 1</i> and <b>ADC006</b> as <i>device 2</i>
The following commands are for <b>ADC005</b>. For <b>ADC006</b>，the commands are exactly the same except for setting <i>priority</i> as <i>100</i>.
```
vcs floating-ip 2.2.2.2 /24
vcs device 1
priority 150
interfaces ve 99
enable
exit
vcs reload
```
After handshaking and synchronisation, VCS had been built up. Since we reloaded <b>ADC006</b> VCS after <b>ADC005</b>, <b>ADC005</b> became <i>ADC005-Standby-vBlade[1/1]</i> and <b>ADC005</b>, <b>ADC006</b> became <i>ADC005-Active-vMaster[1/2]</i>. The VSC status and information could be read by command <i>show vcs summary</i> and <i>show vcs stat</i>.
The following information was the current vcs summary on ADC006.
```
ADC006-Active-vMaster[1/2]#show vcs summary
VCS Chassis:
   VCS Enabled:                               Yes
   Chassis ID:                                1
   Floating IP:                               2.2.2.2
   Mask:                                      255.255.255.0
   Multicast IP:                              224.0.0.210
   Multicast Port:                            41217
   Version:                                   2.7.1-GR1.b58
Members(* means local device):
 ID  State       Priority IP:Port                                       Location
-------------------------------------------------------------------------------
 1   vBlade      150      1.1.1.1:41216                                 Remote  
 2   vMaster(*)  100      1.1.1.2:41216                                 Local   
Total: 2
```
### Upgrade System Version
Before upgrading system, the most important step is to check the current system version and back up system via management interface.
```
show version
backup system use-mgmt-port config ftp://x.x.x.x/
```
As the shown that the system version on both devices was <i>version 2.7.1-GR1, build 58 (Apr-26-2015,07:23)</i> and we would like to upgrade them to <i>4.1.4</i>. We built a FTP server on laptop so that the A10 devices could download <i>.upg</i> system file from laptop via management interface.  
The following sequence is important.
1. Disable VCS on the <i>ADC005-Standby-vBlade[1/1]</i> device.
2. Disable VCS on the <i>ADC006-Active-vMaster[1/2]</i> device.
3. Modified system boot sequence on <i>ADC005-Active</i> then save memory.
4. Upgrade <i>ADC005-Active</i> on hard disk secondary.
5. Modified system boot sequence on <i>ADC006-Active</i> then save memory.
6. Switch <i>ADC006-Active</i> to <i>ADC006-ForcedStandby</i> mode by command <i>vrrp-a force-self-standby</i>, then check VRRP-A by <i>show vrrp-a</i>.
7. Upgrade <i>ADC006-ForcedStandby</i> on hard disk secondary.
8. Disable <i>ForcedStandby</i> by <i>no vrrp-a force-self-standby</i>
9. Re-config VCS on <i>ADC005-Active</i> and <i>ADC006-Standby</i>.
10. Enable VCS on <i>ADC005-Active</i>.
11. Enable VCS on <i>ADC006-Standby</i>. (the <i>vBlade</i> one will reboot while VCS synchronising)
12. Check VCS configuration on both two devices.
```
vcs disable
show bootimage
bootimage hd sec
write memory
write memory secondary
upgrade hd sec use-mgmt-port ftp://x.x.x.x/ ACOS_non_FTA_4_1_4_332.64.upg
vcs enable
show vcs stat
```
Finally we got ACOS 4.1.4,
```
ADC006-Standby#show version
AX Series Advanced Traffic Manager AX3530
  Copyright 2007-2018 by A10 Networks, Inc.  All A10 Networks products are
  protected by one or more of the following US patents:
  9756071, 9742879, 9722918, 9712493, 9705800, 9661026, 9621575, 9609052
  9602442, 9596286, 9596134, 9584318, 9544364, 9537886, 9531846, 9497201
  9477563, 9398011, 9386088, 9356910, 9350744, 9344456, 9344421, 9338225
  9294503, 9294467, 9270774, 9270705, 9258332, 9253152, 9231915, 9219751
  9215275, 9154584, 9154577, 9124550, 9122853, 9118620, 9118618, 9106561
  9094364, 9060003, 9032502, 8977749, 8943577, 8918857, 8914871, 8904512
  8897154, 8868765, 8849938, 8826372, 8813180, 8782751, 8782221, 8595819
  8595791, 8595383, 8584199, 8464333, 8423676, 8387128, 8332925, 8312507
  8291487, 8266235, 8151322, 8079077, 7979585, 7804956, 7716378, 7665138
  7675854, 7647635, 7627672, 7596695, 7577833, 7552126, 7392241, 7236491
  7139267, 6748084, 6658114, 6535516, 6363075, 6324286, RE44701, 8392563
  8103770, 7831712, 7606912, 7346695, 7287084, 6970933, 6473802, 6374300
          64-bit Advanced Core OS (ACOS) version 4.1.4, build 332 (Mar-01-2018,12:37)
          Booted from Hard Disk secondary image
          Number of control CPUs is set to 1
          Serial Number: AX35C03016090005
          aFleX version: 2.0.0
          aXAPI version: 3.0
          Cylance version: N/A
          Hard Disk primary image version 2.7.1-GR1, build 58
          Hard Disk secondary image (default) version 4.1.4, build 332
          Compact Flash primary image (default) version 2.7.1-GR1, build 58
```
* In former versions VCS does not need to re-config after upgrading. However, we found all VCS configurations disappeared in the experiment process. An engineer from A10 supplier gave us the demonstartion that VCS function in ACOS 4.1.4 has been a great improvement comparing with 2.7.1. In the process of upgrading, A10 device will drop former VCS configuration.
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
```
