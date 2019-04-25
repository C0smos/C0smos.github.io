---
date: 2019-04-25
title: Protecting C2 Implants
description: Notes on how to create safe C2 implants
categories: C2
---

## C2 Design Decisions

### Implants
A number of mechanisms exist to ensure that C2 implants are safe and behave in a secure manner to ensure that client environments are protected. The following lists and describes common mechanisms found in C2 implants:

#### Kill Switches

Kill switches can be used to instruct when the C2 implant should desctruct and uninstall itself from the system. It is particularly useful to incorporate this into implants to ensure that the implant is not active outside of client engagement testing windows. Additionally, having the ability to remotely kill the implant is useful, if for instance the engagement needs to be stopped for a particular reason.

#### Target Checks

To avoid the implant executing outside of the defined target a number of questions should be satisfied prior to execution. For instance, whether the particular host is joined to the relevant domain i.e. lab.local. Or whether the host shouldn't be domain joined. Adding checks to ensure that the payload executes against the correct logged in user and that the host is running the specific type of expected operating system can also assist with ensuring that implants execute against in-scope targets.

#### Implant Secure Coding

Implants should be written in memory safe languages such as C# to avoid introducing vulnerabilities into the target host which could be exploited by other attackers. Implants should also avoid logic and misconfiguration vulnerabilities such as implants which may run as services configured with insecure file permissions.

#### Implant Communication and Keying

Implants should always communicate over a secure and encrypted channel when sensitive information is being extracted from the target environment (depending on the type of simulation). Implants can also be designed to only communicate back to specific attacker controlled infrastructure where implant communications can be controlled using URL rewriting and firewall rules. Key derivation functions can be leveraged to ensure that implants are protected by encrypting the implant and using resources such as environment variables or Active Directory properties in order to create the decryption key. It is also possible to use resources remotely, which can be controlled by an attacker and as such allows for greater controlled of when the implant executes or not.