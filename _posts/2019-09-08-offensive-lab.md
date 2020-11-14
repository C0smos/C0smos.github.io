---
date: 2019-09-08
title: Offensive Lab Infrastructure
description: Notes on building offensive labs using AutomatedLab
categories: inf

---
## Offensive Lab using AutomatedLab

The following details notes on the lab I've designed to help when testing out techniques, tools, detections. The lab also serves as a purpose to aid in understanding issues that arise when building infrastructure.

The lab runs on an Intel NUC Hades Canyon with 1TB M2 SSD storage and 64GB RAM. The hypervisor is running Proxmox with the following hosts:

* Exchange Server 2016
* Windows Server 2016 x 2
* Squid Proxy (HTTPS outbound only), no categorisation atm)
* OpenVPN server
* WSUS Server
* Windows 10 clients
* Debian client
* XREF

The lab consists of a DMZ, client and server networks, that are segregated by a PFSense firewall.

The AD environment includes two Forests (bank and dev) which has one parent and one child domain. The following blog post was used as a reference, and is super helpful to quickly standup a useful AD for practicing attacks:

XREF

The DMZ has a Proxmox security mail gateway. A Cisco switch is used to connect the "attacker box" to the DMZ. In order to get access into the client bank VLAN, a phishing payload has to be delivered which will be executed by a "simulated user" (Python script etc), providing a foothold into the environment.