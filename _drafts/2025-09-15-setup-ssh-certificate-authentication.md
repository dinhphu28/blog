---
layout: post
title: How to set up SSH Certificate Authentication with Your Own CA
date: 2025-09-15 17:30:00+0700
description: Set up SSH Certificate Authentication with OpenSSH and your own Certificate Authority (CA) using native SSH tools. Enhance security and simplify management.
mermaid:
  enabled: true
  zoomable: false
tags: ca openssh ssh security pki
categories: utilities
citation: true
giscus_comments: true
toc:
  beginning: true
---

Pain point:
- Authenticate with passwords is insecure and cumbersome.
- TOFU (Trust On First Use) model has security risks.
- When managing multiple servers, distributing and updating public keys is a hassle.
- When a user leaves, you have to remove their keys from all servers.
- You want a more scalable and manageable way to handle SSH authentication.

Solution: SSH Certificate Authentication with Your Own CA
Benefits:
  - Centralized management of user and host keys.
  - Simplified key distribution and revocation.
  - Enhanced security with signed certificates.
  - Easier onboarding and offboarding of users.

How it works:
1. We have Host CA and User CA
2. The Host CA signs the host keys of servers.
3. The User CA signs the user keys of clients.
4. Server trusts the User CA to authenticate users.
5. Client trusts the Host CA to authenticate servers.
6. When a user connects to a server, the server presents its signed host certificate. The client verifies it against the Host CA.
7. The client presents its signed user certificate. The server verifies it against the User CA.

Explain:
- Host authentication
- User authentication

Setup:
1. Generate Host CA and User CA key pairs
2. SSH server generates its host key pair
3. Sign the server's host key with the Host CA
4. Server configures to distribute its host certificate, and trusts the User CA
5. Client generates its user key pair
6. Sign the client's user key with the User CA
7. Client configures to use its user certificate, and trusts the Host CA
8. Test the setup

Advanced:
Below setup is basic, it's suitable for home lab or small business.
Some disadvantages:
- Revocation is not handled well. You need to manage revocation lists manually and distribute them to servers. So big effort.
- Expiration is static. You need to re-issue certificates when they expire. Consider use short-lived certificates, but it takes more effort to distribute them.

Recommended for large scale deployments:
- Using Netflix BLESS or HashiCorp Vault SSH CA to automate certificate issuance and revocation.

