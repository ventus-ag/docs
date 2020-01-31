---
title: Security Guide
description: Provides information about security incidents and how to fix problems. the issues.
tags: [ featured, tutorial ]
# permalink: /Security_Guide/
---
# Security Guide
{: .no_toc }
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## DNS Open-resolver
---
DNS Open-resolvers are DNS servers responding to recursive queries for arbitrary domain names from anywhere on the Internet.

### Problem
DNS Open-resolvers can be abused for DDoS reflection attacks against third parties.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a DNS server is configured as an open resolver allowing recursive queries, you can use the 'dig' tool for sending a DNS request for an arbitrary domain name (the server is not authoritative for) to the IP address of the DNS server in question:
```
$ dig cert-bund.de @192.168.45.67
```

An open resolver allowing recursive queries will return a response like this, followed by a set of DNS records:
```console
; <<>> DiG 9.8.1-P1 <<>> cert-bund.de @192.168.45.67

;; global options: +cmd

;; Got answer:

;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43941

;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
```

A DNS server not allowing recursive queries will instead respond with an error message like this:
```console
; <<>> DiG 9.8.1-P1 <<>> cert-bund.de @192.168.45.67

;; global options: +cmd

;; Got answer:

;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 42022

;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 0

;; WARNING: recursion requested but not available
```

### Solution
Disable recursion or limit recursion to trusted clients in the DNS server's configuration.
For instructions on how to disable/limit recursion with other DNS servers, please consult the manual.

### Further Information
- Wikipedia: [Domain Name System](<https://en.wikipedia.org/wiki/Domain_Name_System>)