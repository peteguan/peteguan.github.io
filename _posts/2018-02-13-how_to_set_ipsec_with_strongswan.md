---
layout: post
title:  "How to set IPSec with Strongswan"
date:   2018-02-13
excerpt: "This tiny project was one of my network security assignments when I was in Bham. It might be replaced by SSL recent years but still a well designed technology"
image: "/images/ipsec/strongswan-vpn.png"
---

## 1	PREPARATION

### 1.1	1.1	UBUNTU VIRTUAL MACHINES

In this experiment, three communicating Linux virtual machines should be available. I chose the latest version of Ubuntu Linux 64-bit installed in Oracle VM VirtualBox named Router, IPsec_1 (A side) and IPsec_2 (B side) shown in the figure below.<br>

![avatar](/images/ipsec/ipsec1_1_1.png){:height="80%" width="80%" .center-image}
<br>

Setting IPv4 address of the Router as 192.168.0.1 at first. Powered up both Linux VPN VM, IPv4 address of A side was set as 192.168.0.5 and 192.168.0.6 for B side.
