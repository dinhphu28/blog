---
layout: post
title: How to set up SSH Certificate Authentication with Your Own CA
date: 2025-09-17 13:00:00+0700
description: Learn how to set up SSH certificate authentication with your own CA to replace key distribution, improve security, and simplify management.
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

**TL;DR**: SSH certificates replace traditional key management with a CA-based model. Hosts and users get signed certificates instead of raw keys, making authentication more secure, scalable, and easier to manage. This guide shows you how to set up your own SSH CA for both host and user authentication.

## Introduction

Managing SSH keys at scale is difficult:

- Password authentication is insecure.
- TOFU (Trust On First Use) introduces risks.
- Distributing and updating keys across servers is a hassle.
- Offboarding requires manual cleanup on every host.

SSH certificate authentication solves these problems by using a **Certificate Authority (CA)** to sign and verify both host and user keys.

## Benefits

- Centralized management of keys
- Simplified distribution and revocation
- Stronger authentication with signatures
- Easier onboarding and offboarding

## How It Works

- **Host CA** signs server keys
- **User CA** signs client keys
- Server trusts the User CA to verify users
- Client trusts the Host CA to verify servers

Connection flow:

1. Server presents signed host certificate → client verifies against Host CA
2. Client presents signed user certificate → server verifies against User CA

## Setup

### 1. Generate CA keys

```sh
ssh-keygen -t ed25519 -f user_ca_key -C dinhphu28_user_ca
ssh-keygen -t ed25519 -f host_ca_key -C dinhphu28_host_ca
```

Keep the private keys secure.

### 2. Host certificate

On the server:

```sh
ssh-keygen -f ssh_host_ed25519_key -N '' -t ed25519
```

Sign with Host CA:

```sh
ssh-keygen -s host_ca_key -I dinhphu28-mac-machine -h \
  -n "example-host.dinhphu28.com,10.8.0.6" -V +365d ssh_host_ed25519_key.pub
```

### 3. Configure server

`/etc/ssh/sshd_config`:

```ini
HostKey /usr/etc/ssh/ssh_host_ed25519_key
HostCertificate /usr/etc/ssh/ssh_host_ed25519_key-cert.pub
TrustedUserCAKeys /usr/etc/ssh/user_ca_key.pub
```

Restart sshd:

```sh
sudo systemctl restart sshd
```

### 4. User certificate

On the client:

```sh
ssh-keygen -t ed25519 -C "robert@dinhphu28.com"
```

Sign with User CA:

```sh
ssh-keygen -s user_ca_key -I "robert@dinhphu28.com" -n "robert" -V +1d id_ed25519.pub
```

### 5. Configure client

Move cert to `~/.ssh/`.
Add Host CA to `~/.ssh/known_hosts`:

```ini
@cert-authority *.dinhphu28.com,10.8.0.6 ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFraYAO41YSLterrzte7TknguJWpTrCNC8MCL6xbcvbw
```

### 6. Test

```sh
ssh-keygen -L -f /path/to/your-cert.pub
ssh -T robert@example-host.dinhphu28.com
```

## Advanced

Limitations of the basic setup:

- Revocation is manual
- Certificates have static expiration

For larger environments, use automated solutions:

- [Netflix BLESS](https://github.com/Netflix/bless)
- [HashiCorp Vault SSH CA](https://developer.hashicorp.com/vault/docs/secrets/ssh)

## Conclusion

SSH certificate authentication provides scalable and secure management of users and hosts. With your own CA, you eliminate TOFU, simplify key distribution, and make onboarding/offboarding easier.
