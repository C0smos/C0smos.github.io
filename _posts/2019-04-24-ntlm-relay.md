---
date: 2019-04-24
title: NTLM Relay Techniques
description: Ideas for performing NTLM relay attacks
categories: LateralMovement
---

A number of systems within an environment allow for Kerberos authentication to occur within an AD environment, which can be leveraged to escalate privileged and move laterally across a network. The idea is to force a service which runs as a privileged domain user to connect back to an attacker controlled SMB server, where the NTLMv2 hash can be relayed onto another machine where the domain user running the service has privileges. The following is documentation which describes different examples of how this technique could be leveraged.

### Oracle NTLMv2 Relaying

### MS-SQL NTLMv2 Relaying
If the SQL server instance permits authentication with a user that has the ability to perform XP type commands such as xp_dirtree, it is possible to capture the NTLMv2 of the domain user running the MSSQL service. The following describes this technique:

XREF

### Management Interfaces
Any type of mangement system which can be accessed, for example by a web interface could be leveraged, as these systems typically require high privileged to perform actions such as updates across systems within an environment, and can also be leveraged to relay an NTLMv2 hash.
