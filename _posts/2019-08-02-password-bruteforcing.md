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

## Password Cracking
Password cracking can be done using a number of approaches, such as combining wordlists together, using keyboard walks or brute forcing passwords.

Hashcat can be used to perform both brute force and dictionary based password guessing attacks to crack hashes. The following can be used to achieve this:

```
hashcat -m 18200 hash.txt -a 6 /Volumes/scratch/combo_hashcat.txt "?d?d?d?d" -O
```
The command above will add 4 extra digits to the combo_hashcat.txt wordlist and start attempting to crack the hash.

Combinators can also be used, to enhance wordlists as previously mentioned. The following command can be carried out to achieve this:

```
hashcat-utils/src/combinator.bin SecLists/Passwords/Common-Credentials/100k-most-used-passwords-NCSC.txt route2.txt > /Volumes/scratch/combo_hashcat.txt
```

To make wordlists even more creative it is possible to leverage the kwprocessor from Hashcat to generate a wordlist based on keyboard walks. The wordlist "route2.txt" is a collection of keyboard walks generated from kwp. The following can be done to create a keyboard walk wordlist.

```
./kwp basechars/full.base keymaps/en-us.keymap routes/4-to-4-exhaustive.route >> /Volumes/scratch/route2.txt

```