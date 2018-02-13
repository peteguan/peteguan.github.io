---
layout: post
title:  "How to set IPSec with Strongswan"
date:   2018-02-13
excerpt: "This tiny project was one of my network security assignments when I was in Bham. It might be replaced by SSL recent years but still a well designed technology"
image: "/images/ipsec/strongswan-vpn.png"
---
<b>Internet Protocol security</b> (IPSec) is a framework of open standards for helping to ensure private, secure communications over Internet Protocol (IP) networks through the use of cryptographic security services. IPSec supports network-level data integrity, data confidentiality, data origin authentication, and replay protection. Because IPSec is integrated at the Internet layer (layer 3), it provides security for almost all protocols in the TCP/IP suite, and because IPSec is applied transparently to applications, there is no need to configure separate security for each application that uses TCP/IP.

IPSec helps provide defense-in-depth against:

* <b>Network-based attacks from untrusted computers, attacks that can result in the denial-of-service of applications, services, or the network</b>

* <b>Data corruption</b>

* <b>Data theft</b>

* <b>User-credential theft</b>

* <b>Administrative control of servers, other computers, and the network.</b>

You can use IPSec to defend against network-based attacks through a combination of host-based IPSec packet filtering and the enforcement of trusted communications.

[<i><b>strongSwan</b></i>](https://www.strongswan.org/download.html) is a open source IPsec-based VPN solution, runs on Linux 2.6, 3.x and 4.x kernels, Android, FreeBSD, OS X, iOS and Windows, which could implements both the <i>IKEv1</i> and <i>IKEv2 (RFC 7296)</i> key exchange protocols. It could be downloaded by clicking https://www.strongswan.org/download.html.
## 1	PREPARATION

###	1.1	UBUNTU VIRTUAL MACHINES

In this experiment, three communicating Linux virtual machines should be available. I chose the latest version of Ubuntu Linux 64-bit installed in Oracle VM VirtualBox named <b>Router</b>, <b>IPsec_1</b> <i>(A side)</i> and <b>IPsec_2</b> <i>(B side)</i> shown in the figure below.<br>

![avatar](/images/ipsec/ipsec1_1_1.png){:height="80%" width="80%" .center-image}
<br>

Setting IPv4 address for the Router as 192.168.0.1 at first. Powered up both Linux VPN VM, IPv4 address of A side was set as 192.168.0.5 and 192.168.0.6 for B side.<br>
![avatar](/images/ipsec/ipsec1_1_2.png){:height="50%" width="43%" }
![avatar](/images/ipsec/ipsec1_1_4.png){:height="50%" width="43%" }
<br><br>


###	1.2	STRONGSWAN INSTALLATION & CONFIGURATION

After updating the operate system, the next step is to install <b>StrongSwan</b>.<br>
Commands should be input under <i>root</i> permission.
```
aptitude install strongswan
```
Several libraries and tools also need to be installed for Strongswan compilation. I chose to install <i>Opensc</i> (supporting of HSM in strongswan), <i>GMP Library</i> (supporting mathematical operation in strongswan) and <i>OpenSSL libcrypto</i> tool (implementation of cryptography algorithms) by using commands below:
```
apt-get install opensc
apt-get install libgmp10
apt-get install libgmp-dev
apt-get install libssl-dev
```
Then I downloaded strongswan-5.5.0 to the folder <i>/usr/src/</i>.
![avatar](/images/ipsec/ipsec1_1_5.png){:height="80%" width="80%" .center-image}<br>
Extracted the downloaded file, checked files inside the folder and then ran script to enable HSM support and openssl support.
![avatar](/images/ipsec/ipsec1_1_6.png){:height="80%" width="80%" .center-image}<br>
Used commands <i>make</i> and <i>make install</i> to compile and install strongswan under <i>/usr/local/</i> directory. I did the same operation in both of A side and B side VM so that they could support tunnel mode.
## 2	PRE-SHARED KEY BASED TUNNEL

### 2.1 A side

The <i>ipsec.conf</i> file in A side shows below, Cipher suite was chosen <i>AES256-SHA2_256</i>.
![avatar](/images/ipsec/ipsec2_1_1.png){:height=30%" width="40%" .center-image}
<br>
Then set Pre-Shared key as <i>“ipsec”</i> in the file <i>ipsec.secrets</i> in the path <i>/etc/</i>
![avatar](/images/ipsec/ipsec2_1_2.png){:height="40%" width="40%" .center-image}
<br>
### 2.2 B side
The <i>ipsec.conf</i> file of B side shows below,
![avatar](/images/ipsec/ipsec2_2_1.png){:height=30%" width="40%" .center-image}
<br>
Then set Pre-Shared key as <i>“ipsec”</i> in the file <i>ipsec.secrets</i> in the path <i>/etc/</i>
![avatar](/images/ipsec/ipsec2_2_2.png){:height="40%" width="40%" .center-image}
<br>
### 2.3	RESTART CHARON

Using commands to restart the <i>Charon</i> daemon and view the VPN status in both A side & B side
![avatar](/images/ipsec/ipsec2_3_1.png){:height="70%" width="48%" }
![avatar](/images/ipsec/ipsec2_3_2.png){:height="130%" width="48%" }
<br>
Now the connection had been established!

## 3	X.509 CERTIFICATE BASED TUNNEL
### 3.1 A side
This part is about the Public Key Authentication certificate based tunnel. I used commands to create self-sign certificate and results shown below.
![avatar](/images/ipsec/ipsec3_1_1.png){:height=80%" width="80%" .center-image}
<br>
The next step was modificating configuration files <i>ipsec.conf</i> and <i>ipsec.secrets</i>
![avatar](/images/ipsec/ipsec3_1_2.png){:height=40%" width="40%" .center-image}
<br>
![avatar](/images/ipsec/ipsec3_1_3.png){:height=40%" width="40%" .center-image}
<br>
### 3.2 B side
Then I did similar operations in B side like this,<br>
![avatar](/images/ipsec/ipsec3_2_1.png){:height=80%" width="80%" .center-image}
<br>
![avatar](/images/ipsec/ipsec3_2_2.png){:height=40%" width="40%" .center-image}
<br>
![avatar](/images/ipsec/ipsec3_2_3.png){:height=40%" width="40%" .center-image}
<br>
### 3.3 Test
I restarted IPSec for both sides, then read the ipsec status. The results below shown that the connection was established successfully.
![avatar](/images/ipsec/ipsec3_3_1.png){:height=80%" width="80%" .center-image}
<br>
![avatar](/images/ipsec/ipsec3_3_2.png){:height=80%" width="80%" .center-image}
<br>

## 4	IPSEC.CONF FILE
### 4.1 A side
![avatar](/images/ipsec/ipsec4_1_1.png){:height=70%" width="70%" .center-image}

### 4.2 B side
![avatar](/images/ipsec/ipsec4_2_1.png){:height=70%" width="70%" .center-image}
<br>
<br>
<br><br>
<br><br>
<br><br>
<br>ref:   https://technet.microsoft.com/en-us/library/cc776369(v=ws.10).aspx
