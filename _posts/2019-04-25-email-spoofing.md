---
date: 2019-04-24
title: Email Spoofing Techniques
description: Notes on email spoofing and anti-spoofing techniques
categories: InitialCompromise
---

## Email Spoofing
Initial compromises typically result from phishing campaigns where a subset of users are targeted through the use of email and engineered into performing actions on behalf of an attacker, for instance by running some malicious code unknowingly. For these types of attacks to be successful an attacker would usually attempt to forge an email which appears to come from a legitimate source such as another employee by spoofing the "header-from" email header and using company email signatures.

### Email Spoofing using SMTP
The following shows an example of how to use netcat to send a spoofed email to a specific target:

```
MAIL FROM:<realadmin@mx01.lab.local>
250 2.1.0 Ok
RCPT TO:<wes@mx01.lab.local>
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
From: [Actual Admin] <attacker@example.com>
To: [Wes IT] <wes@mx01.lab.local>
Date: Sat, 27 Apr 2019 15:11
Subject: Phishing training

Hi Wes, hope you're well, please find below the URL containing the phishing training material for this afternoon:

www.wesrenshaw.com/phishing.html

Any questions, let me know,

Admin
```

## Sender Policy Framework (SPF)
SPF is used to check that emails are sent from the specified mail server by performing validations against the envelope-from address where the address value comes from the domain's DNS TXT record. The following SPF record would only permit email which comes from the mail server at "172.16.0.2":

```
mx01.lab.local.		604800	IN	TXT	"v=spf1 ipv4:172.16.0.2 -all"
```

All other email sent from outside this host would be rejected. A soft fail can be configured where email is still recieved however it is then marked as spam:

```
mx01.lab.local.		604800	IN	TXT	"v=spf1 ipv4:172.16.0.2 ~all"
```

As SPF only verifies the envelope sender (MAIL FROM) address and not the header-from address.

SPF logs from postfix:

```
Apr 27 14:26:37 mx01 policyd-spf[13798]: prepend Received-SPF: None (mailfrom) identity=mailfrom; client-ip=172.16.0.10; helo=mx01.lab.local; envelope-from=admin@mx01.lab.local; receiver=<UNKNOWN>
```

Emails which trigger the SPF record, will contain the following email header:

```
Return-Path: <attacker@example.com>
X-Original-To: wes@mx01.lab.local
Delivered-To: wes@mx01.lab.local
Received-SPF: Fail (mailfrom) identity=mailfrom; client-ip=172.16.0.10; helo=example.com; envelope-from=attacker@example.com; receiver=<UNKNOWN>
Received: from unknown (unknown [172.16.0.10])
        by mx01.lab.local (Postfix) with SMTP id 5E93F204B8
        for <wes@mx01.lab.local>; Sat, 27 Apr 2019 15:30:49 +0100 (BST)
X-Mailbox-Line: From [IT Admin]<itadmin@mx01.lab.local>

To [Wes]<wes@mx01.lab.local>
Subject Phishing Training

Hi Wes, hope you're well, please find below the URL containing the phishing training material for this afternoon:

www.wesrenshaw.com/phishing.html

Any questions, let me know

IT Admin
```

When SPF checks actually pass, the following header will exist, permitting email from being recieved to the target:

```
Return-Path: <admin@mx01.lab.local>
X-Original-To: wes@mx01.lab.local
Delivered-To: wes@mx01.lab.local
Received-SPF: None (mailfrom) identity=mailfrom; client-ip=172.16.0.10; helo=mx01.lab.local; envelope-from=admin@mx01.lab.local; receiver=<UNKNOWN>
Received: from unknown (unknown [172.16.0.10])
        by mx01.lab.local (Postfix) with SMTP id 08B25204B8
        for <wes@mx01.lab.local>; Sat, 27 Apr 2019 15:46:21 +0100 (BST)
X-Mailbox-Line: From [IT Admin]<admin@mx01.lab.local>

To [Wes]<wes@mx01.lab.local>
Subject: Phishing

Please watch these phishing video trainings,

Thanks
```

However as the attacker doesn't actually have access to the mailbox at "admin@mx01.lab.local", they won't recieve any emails.

SPF can be bypassed by setting up a domain which has valid SPF records associated where emails are sent from the domain. SPF alone does not provide any means to prevent spoofing.

## Sender ID

## DomainKeys Identified Mail (DKIM)


## Domain Message Authentication Reporting and Conformance (DMARC)
