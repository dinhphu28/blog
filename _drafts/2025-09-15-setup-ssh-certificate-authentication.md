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
   ```sh
   ssh-keygen -t ed25519 -f user_ca_key -C dinhphu28_user_ca
   ssh-keygen -t ed25519 -f host_ca_key -C dinhphu28_host_ca
   ```

   Note: Keep the private key of both CA secure and separated.

2. SSH server generates its host key pair
   ```sh
   ssh-keygen -f ssh_host_ed25519_key -N '' -t ed25519
   ```

   Transfer the new  public key `ssh_host_ed25519_key.pub` to the Host CA

3. Sign the server's host key with the Host CA
   ```sh
   ssh-keygen -s host_ca_key -I dinhphu28-mac-machine -h -n "example-host.dinhphu28.com,10.8.0.6" -V +365d ssh_host_ed25519_key.pub
   ```

   We have the certificate `ssh_host_ed25519_key-cert.pub`, transfer it back to the host.

4. Server configures to distribute its host certificate, and trusts the User CA
   Edit the `/etc/ssh/sshd_config` or add file `/usr/etc/ssh/sshd_config.d/ssh-auth-ca.conf` if using openSUSE Tumbleweed.

   ```properties
   HostKey /usr/etc/ssh/ssh_host_ed25519_key
   HostCertificate /usr/etc/ssh/ssh_host_ed25519_key-cert.pub
   
   TrustedUserCAKeys /usr/etc/ssh/user_ca_key.pub
   ```

   > HostKey and HostCertificate are using for host authentication.
   >
   > TrustedUserCAKeys are using for user authentication.

   Then restart `sshd` with

   ```sh
   sudo systemctl restart sshd
   ```

5. Client generates its user key pair
   ```sh
   ssh-keygen -t ed25519 -C "robert@dinhphu28.com"
   ```

   Transfer the `id_ed25519.pub` to User CA.

6. Sign the client's user key with the User CA
   ```sh
   ssh-keygen -s user_ca_key -I "robert@dinhphu28.com" -n "robert" -V +1d id_ed25519.pub
   ```

   Then we have the user certificate `id_ed25519-cert.pub`, transfer it to client (robert's machine).

7. Client configures to use its user certificate, and trusts the Host CA
   Move `id_ed25519-cert.pub` to `~/.ssh/` (same location of private key).
   Add User CA certificate (content of `host_ca_key.pub`) to `~/.ssh/known_hosts` as below

   ```properties
   @cert-authority *.dinhphu28.com,10.8.0.6 ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFraYAO41YSLterrzte7TknguJWpTrCNC8MCL6xbcvbw
   ```

8. Test the setup
   If you want to see the contents of a certificate (user or host)

   ```sh
   ssh-keygen -L -f /path/to/your-cert.pub
   ```

   Test to connect

   ```sh
   ssh -T robert@example-host.dinhphu28.com
   ```

Advanced:
Below setup is basic, it's suitable for home lab or small business.
Some disadvantages:

- Revocation is not handled well. You need to manage revocation lists manually and distribute them to servers. So big effort.
- Expiration is static. You need to re-issue certificates when they expire. Consider use short-lived certificates, but it takes more effort to distribute them.

Recommended for large scale deployments:
- Using Netflix BLESS or HashiCorp Vault SSH CA to automate certificate issuance and revocation.

