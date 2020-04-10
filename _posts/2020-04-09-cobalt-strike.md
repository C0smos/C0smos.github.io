---
date: 2020-04-09 23:00:00 +0000
title: Cobalt Strike
description: Quick notes on Cobalt Strike
categories: misc

---
##### Note:

This is a post migrated over from notes on CS 3.4, as such a number of these settings below may not be applicable anymore.

### Team Servers and General Setup

Assuming redirectors are all setup correctly, the general configuration is to run the teamserver on a server sitting behind the redirectors and not exposed to the internet:

    teamserver ip password ./c2_profile

Cobalt Strike's client comes in a few formats, Linux, Mac OS and Windows. The team server is only available from Linux however. Running the client from Windows is a good option.

### Listeners

* SMB Bind Pipe for use with smb lateral movement


* HTTPS Beacons 

### Profiles

A number of example profiles exist online, which can be tailored for particular needs.

### Targets tab

This tab can be used to add a target to Cobalt Strike, which can be useful when the target is in another network which can only be reached through another host via SMB bind piping. When the target is added into Cobalt a number of options exist to get a beacon running from it, such as traditional psexec and psexec which leverages PowerShell. In order for this to work however, a privileged beacon needs to be accessible to perform the token manipulation process. Choose a beacon where Administrator privileges are running.

### Beacon

Cobalt beacons work by suspending a running process, and injecting the beacon shellcode and then resuming the process again. Beacon has a large number of interesting commands which can be used to perform tasks againsts hosts.

* execute-assembly .net_exe
* powershell-import
* powerpick Powershell_command

If escalating to SYSTEM if required in order to dump password hashes for instance, then the following can be performed against a beacon running in a high integrity Administrator process:

    Access > Elevate > uac_token_duplication

To migrate from a x86 process, inject a beacon into a x64 bit process.

### Socks Pivoting

Within each beacon, there's an option to run a Socks server, to allow for pushing traffic through the compromised host. For more information on pivoting and proxying, please refer to the lateral movement section.

### Downloading / Uploading

Beacon has a nice way to download and upload files, to download files just run download filename. The file will then be in the Cobalt Strike's client download tab and then files can be synced to the local computer where the client is running.