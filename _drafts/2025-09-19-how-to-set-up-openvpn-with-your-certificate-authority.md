---
layout: post
title: How to set up OpenVPN server with your own Certificate Authority Infrastructure
date: 2025-09-17 13:00:00+0700
description: A step-by-step guide to setting up an OpenVPN server using your own Certificate Authority (CA) for secure and scalable VPN access.
mermaid:
  enabled: true
  zoomable: false
tags: ca security pki openvpn ssl
categories: utilities
giscus_comments: true
toc:
  beginning: true
---

- At the post [Build a Root and Intermediate Certificate Authority with Easy-RSA]({% post_url 2025-07-14-build-a-root-and-intermediate-certificate-authority-with-easy-rsa %}), we have built a Root CA and an Intermediate CA.
Now, we will set up an OpenVPN server using the Intermediate CA to issue client certificates.

- Prerequisites:
  - A server with OpenVPN installed (e.g., Ubuntu, Debian, ...).
  - Easy-RSA installed on the server.
  - Root CA and Intermediate CA set up as per the [previous post]({% post_url 2025-07-14-build-a-root-and-intermediate-certificate-authority-with-easy-rsa %}). You should create new Intermediate CA for OpenVPN if you want to separate it from other services.

In case you haven't install OpenVPN yet, you can follow the official guide: [OpenVPN Installation Guide](https://openvpn.net/community-docs/installing-openvpn.html). You can also install it via package manager like Debian (APT) and RPM.

Ubuntu/Debian-based:

```sh
sudo apt update
sudo apt install openvpn
```
Create Server Cert:
- Go to server's Easy-RSA directory (e.g., `cd ~/easy-rsa`).

```sh
./easyrsa init-pki
./easyrsa gen-req server nopass
```

- Copy the private key to the server's OpenVPN directory:

```sh
sudo cp ~/easy-rsa/pki/private/server.key /etc/openvpn/
```

Request Server Cert from Intermediate CA:

Send the `server.req` file to the Intermediate CA machine and sign it. `server.req` is located at `~/easy-rsa/pki/reqs/server.req`.

Sign server request on Intermediate CA machine:

```sh
./easyrsa import-req /path/to/server.req server
./easyrsa sign-req server server
```

Send the signed certificate back to the OpenVPN server. The signed certificate is located at `~/easy-rsa/pki/issued/server.crt`.

Remember to also copy the CA Chain Certificate from the Intermediate CA machine to the OpenVPN server. The CA Chain Certificate is located at `~/easy-rsa/pki/ca-chain.crt` as mentioned in the previous post. Or you can create a new one by concatenating the Intermediate CA certificate and the Root CA certificate:

```sh
cat /path/to/root-ca.crt path/to/intermediate-ca.crt > ca-chain.crt
```

Copy `server.crt`, `ca-chain.crt` to OpenVPN directory:

```sh
sudo cp /path/to/server.crt /etc/openvpn/
sudo cp /path/to/ca-chain.crt /etc/openvpn/
```

Generate Diffie-Hellman key:
```sh
./easyrsa gen-dh
sudo cp ~/easy-rsa/pki/dh.pem /etc/openvpn/
```

Generate HMAC key for additional security:

```sh
openvpn --genkey --secret ta.key
sudo cp ta.key /etc/openvpn/
```

Config OpenVPN service:

```sh
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf /etc/openvpn/
```

Change or add below values in `/etc/openvpn/server.conf`:

```sh
tls-auth ta.key 0
cipher AES-256-GCM # Better than AES-256-CBC
auth SHA256
dh dh.pem
user nobody
group nogroup
ca ca-chain.crt

push "redirect-gateway def1 bypass-dhcp" # Redirect all traffic through VPN
push "dhcp-option DNS 208.67.222.222" # OpenDNS
push "dhcp-option DNS 208.67.220.220" # OpenDNS
```

Adjust the server's networking:

Allow IP forwarding

`/etc/sysctl.conf`
```sh
net.ipv4.ip_forward=1
```

```sh
sudo sysctl -p
```

Setup NAT & firewall rules:

In this post, we will use `iptables`. You can use `ufw` or `firewalld` if you prefer.



Start and enable OpenVPN systemd service:

```sh
sudo systemctl start openvpn@server
sudo systemctl enable openvpn@server
```

To check the status of the OpenVPN service:
```sh
systemctl status openvpn@server
```

Setup OpenVPN Client Configuration:

```sh
mkdir ~/openvpn-clients
chmod 700 ~/openvpn-clients
mkdir ~/openvpn-clients/keys
mkdir ~/openvpn-clients/profiles
cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf ~/openvpn-clients/base.conf
```

Change or add below values in `~/openvpn-clients/base.conf`:

```sh
remote your-server.dinhphu28.com 1194
user nobody
group nogroup
cipher AES-256-GCM # Better than AES-256-CBC
auth SHA256
key-direction 1
```

Remove below lines:
```sh
ca ca.crt
cert client.crt
key client.key
tls-auth ta.key 1
```

Add below lines:

```sh
; script-security 2
; up /etc/openvpn/update-resolv-conf
; down /etc/openvpn/update-resolv-conf

; script-security 2
; up /etc/openvpn/update-systemd-resolved
; down /etc/openvpn/update-systemd-resolved
; down-pre
; dhcp-option DOMAIN-ROUTE .
```

Create script to generate client profile: `~/openvpn-clients/build_profile.sh`

```sh
chmod +700 ~/openvpn-clients/build_profile.sh
```

Add below lines to `build_profile.sh`:
```sh
#!/bin/bash

KEY_DIR=/home/myserver/openvpn-clients/keys
OUTPUT_DIR=/home/myserver/openvpn-clients/profiles
BASE_CONFIG=/home/myserver/openvpn-clients/base.conf

cat ${BASE_CONFIG} \
    <(echo -e '<ca>') \
    ${KEY_DIR}/ca-chain.crt \
    <(echo -e '</ca>\n<cert>') \
    ${KEY_DIR}/${1}.crt \
    <(echo -e '</cert>\n<key>') \
    ${KEY_DIR}/${1}.key \
    <(echo -e '</key>\n<tls-auth>') \
    ${KEY_DIR}/ta.key \
    <(echo -e '</tls-auth>') \
    > ${OUTPUT_DIR}/${1}.ovpn
```

Create Client Profile:

- Generate client key and CSR on the OpenVPN server:
- Send the CSR to the Intermediate CA to sign it.
- Copy the signed certificate back to the OpenVPN server.
- Copy the client's private key and certificate to `~/openvpn-clients/keys`
- Build the client profile using the script:
```sh
sudo ./build_profile.sh client_alice
```

The client profile will be located at `~/openvpn-clients/profiles/client_alice.ovpn`.

Distribute the `.ovpn` file to the client securely.

