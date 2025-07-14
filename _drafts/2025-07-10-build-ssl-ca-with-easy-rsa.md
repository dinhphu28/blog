---
layout: post
title: Build your own SSL Certificate Authority with Easy-RSA
date: 2025-05-30 15:09:00+0700
description: Setting up your own SSL Root CA and Intermediate CA for home lab or your organization.
tags: ssl ca easy-rsa openssl security
categories: utilities
citation: true
giscus_comments: true
toc:
  beginning: true
---

- Manage large number of users or devices in the organization environment is a challenge to:

  - Data security and privacy.
  - Trust.
  - Data and system integrity.
  - Automation and management.
  - When phishing resistance is crucial.

- Some use case:

  - Securing web servers
  - VPNs
  - Email
  - IoT devices
  - Cloud services
  - Mutual Authentication

- Scalability and management:

  - Large user bases (using various resources)

- Why not use self-signed certificate or public CA?

  - Self-signed certificate:
    - Not trusted by default.
    - Hard to manage.
  - Public CA:
    - Costly.
    - Limited control over the certificate lifecycle.

- Solution: Build your own SSL Certificate Authority (CA) with Easy-RSA.

  - Build Root CA and Intermediate CA.

- Two-tier CA hierarchy:
  - Root CA: The top-level CA that signs the Intermediate CA.
  - Intermediate CA: Signs end-entity certificates.

Note that: The Root CA should be kept offline and only used to sign the Intermediate CA.
The Intermediate CA can be used to sign end-entity certificates.

- My case:

  - I have a home lab with multiple devices and services.
    - OpenVPN server
    - Microsoft 365
    - Web servers
    - Email security (S/MIME)
  - I feel headache when add a new user or device to the system.

- Next article: How I manage a large number of users and servers SSH access
