---
date: 2019-09-08
title: Offensive Lab Infrastructure
description: Notes on building offensive labs using AutomatedLab
categories: inf
---

## Offensive Lab using AutomatedLab
AutomatedLab is an amazing framework for deploying complex labs using PowerShell to Azure or a Hyper-V Server. A number of issues did come up when deploying an offensive lab using AutomatedLab, this was mostly due to PSRemoting and credential issues. By modifying the AutomatedLab PowerShell module to remove "-DoNotUseCredSsp:$DoNotUseCredSs" which is passed into the "New-LabPSSession" cmdlet the connecting issues disapear.

The current set up with segregation relies on Firewalls being placed into the environment, these firewalls need to route traffic to and from networks for instance the server and workstation networks. PFSense will be used to achieve this, and to include an element of automated an XML file will be created which can be re-imported. Once this is done the script to deploy the offensive lab can then be ran.