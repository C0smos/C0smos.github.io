---
date: 2019-05-26
title: Privilege Escalation Techniques on Windows
description: Techniques for Escalating Privileges on Windows Hosts
categories: PrivilegeEscalation
---

## COM
Middleware

Can we inject our own COM shellcode into a Word doc, through Excel?

IUnknown Interface:
    - QueryInterface method (solve casting problem), GUID,
    - AddRef
    - Release 

COM has a registration system - uses Windows Registry (HKEY_CLASSES_ROOT)
    - 
COM Apartments
    - call init function, apartment threading (single threaded apartment model - container) 
        - call methods from the same thread - implemented for OLE, GUI based
        - Use proxy to call between threads (apartments)
### DCOM

## Discovering privilege escalation bugs
* Tools to run to identify privileged processes accessing low privileged user controlled items
    - procmon
    - iLSpy
    - Ida Pro / GHidra
    - NtAPI, TokenViewer, OleView (All Forshaw's Sandbox tools)

### Enumerating Privileged Processes

### File Access Primitives