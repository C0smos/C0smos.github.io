---
date: 2019-08-02
title: Outlook Password Bruteforcing
description: Notes for password bruteforcing
categories: InitialCompromise
---

## Password Attacks Against OWA EWS
Password Bruteforce attacks against OWA EWS:

To generate a wordlist which suits users within an organisation better (default domain password policy and common organisation passwords), hashcat-utils combinator tool can be used to combine two wordlists together, for instance one wordlist which contains common words such as seasons and months and the other wordlist can contain numbers ranging from two to four for instance 2019. To acheive this the seq tool can be used:

```
seq 00 99 > twonumbers.txt
seq 0000 9999 > fournumbers.txt
```

Then the combinator tool can be used to combine these together:

```
./combinator words.txt fournumbers.txt > passlist.txt
```