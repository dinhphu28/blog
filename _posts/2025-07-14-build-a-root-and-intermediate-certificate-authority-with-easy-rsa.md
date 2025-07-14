---
layout: post
title: Build a Root and Intermediate Certificate Authority with Easy-RSA
date: 2025-07-14 18:00:00+0700
description: Step-by-step guide to building a Root CA and Intermediate CA using Easy-RSA, including issuing certificates for Microsoft 365 authentication.
tags: ssl ca easy-rsa openssl security tutorial
categories: utilities
citation: true
giscus_comments: true
toc:
  beginning: true
---

> 📖 This post continues from [Part 1: Why Build Your Own Certificate Authority](/why-build-your-own-certificate-authority)

## 📁 Root CA Setup

### 1. Download and Extract Easy-RSA

Optionally rename the directory to `root-ca`.

### 2. Create or Edit the `vars` File

```sh
set_var EASYRSA_REQ_COUNTRY    "VN"
set_var EASYRSA_REQ_PROVINCE   "Ho Chi Minh City"
set_var EASYRSA_REQ_CITY       "Ho Chi Minh City"
set_var EASYRSA_REQ_ORG        "DINHPHU28 Root CA"
set_var EASYRSA_REQ_EMAIL      "ca@dinhphu28.com"
set_var EASYRSA_REQ_OU         "Community"
set_var EASYRSA_ALGO           "ec"
set_var EASYRSA_DIGEST         "sha512"
set_var EASYRSA_CURVE          "secp384r1"
```

### 3. Initialize the PKI

```sh
./easyrsa init-pki
```

### 4. Build the Root CA

```sh
./easyrsa build-ca
```

> 🔐 Remember the password — this secures your CA.

You now have:

- `pki/private/ca.key`
- `pki/ca.crt` (Root certificate)

### 5. Create Custom x509 Type for Intermediate CA

Create `x509-types/caRestricted`:

```properties
basicConstraints = critical, CA:TRUE, pathlen:0
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
keyUsage = critical, cRLSign, keyCertSign
```

## 🧷 Intermediate CA Setup

Repeat steps 1–3 in a new directory like `intermediate-ca`, then update `vars`:

```sh
set_var EASYRSA_REQ_ORG        "DINHPHU28 EC CA"
set_var EASYRSA_REQ_OU         "Intermediate"
```

### Initialize and Create Request

```sh
./easyrsa init-pki
./easyrsa gen-req intermediate-ca
```

Transfer `intermediate-ca.req` to the **offline Root CA**.

### At the Root CA

```sh
./easyrsa import-req /path/to/intermediate-ca.req intermediate-ca
EASYRSA_NO_SAN=1 ./easyrsa sign-req caRestricted intermediate-ca
```

### Back at Intermediate CA

```sh
cp /path/to/intermediate-ca.crt pki/ca.crt
cp /path/to/root-ca/pki/ca.crt pki/root-ca.crt
cat pki/ca.crt pki/root-ca.crt > pki/ca-chain.crt
```

✅ You now have a functional Intermediate CA.

## 📜 Issue Certificate for Microsoft 365 Authentication

### 1. Create OpenSSL Config

Save as `user_openssl.cnf`:

```properties
[ req ]
default_bits       = 384
prompt             = no
distinguished_name = dn
req_extensions     = req_ext
string_mask        = utf8only
default_md         = sha512

[ dn ]
CN = Dinh Phu Nguyen

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
otherName = 1.3.6.1.4.1.311.20.2.3;UTF8:dinhphu@office.dinhphu28.com
```

### 2. Generate Key and CSR

```sh
openssl ecparam -name secp384r1 -genkey -noout -out dinhphu.key
openssl req -new -key dinhphu.key -out dinhphu.csr -config user_openssl.cnf
```

### 3. Sign the CSR at Intermediate CA

```sh
./easyrsa import-req /full/path/to/dinhphu.csr dinhphu
EASYRSA_CERT_EXPIRE=60 ./easyrsa --subject-alt-name="otherName:1.3.6.1.4.1.311.20.2.3;UTF8:dinhphu@office.dinhphu28.com" sign-req client dinhphu
```

### 4. Verify UPN

```sh
openssl x509 -in pki/issued/dinhphu.crt -noout -text | grep -A5 "Subject Alternative Name"
```

Should contain your UPN like this:

```properties
X509v3 Subject Alternative Name:
    otherName:1.3.6.1.4.1.311.20.2.3;UTF8:dinhphu@office.dinhphu28.com
```

**Note:** Above client cert `dinhphu.crt` is only contain client cert.

To verify the client cert valid, we not only need the Root CA cert but also Intermediate CA (full chain of trust):
`Root CA -> Intermediate CA -> Client cert`

Most of SSL Tools or System use use client cert with fullchain (intermediate-a -> leaf) to verify the client with only Root CA cert.

```sh
cat /path/to/intermediate-ca/pki/ca.crt dinhphu.crt > dinhphu-fullchain.crt
```

## 📦 Create PFX Certificate

```sh
openssl pkcs12 -export \
  -inkey dinhphu.key \
  -in dinhphu.crt \
  -certfile /path-to/ca-chain.crt \
  -out dinhphu.pfx \
  -name "Dinh Phu Nguyen"
```

> You'll be prompted for a password to protect the `.pfx`.

### Optional: macOS Conversion

Because of this `pfx` using pkcs12 is not compatible with macOS, so we need to convert it to legacy.

```sh
openssl pkcs12 -in dinhphu.pfx -out dinhphu.pem
openssl pkcs12 -export -in dinhphu.pem -out dinhphu_macos.pfx -legacy
```

## 🏢 Microsoft Entra Configuration Notes

### Authentication Binding

| Certificate Attribute   | Value                    |
| ----------------------- | ------------------------ |
| Policy OID              | `1.3.6.1.4.1.311.20.2.2` |
| Authentication strength | Multi-factor             |
| Affinity binding        | Low                      |

**Add rule:** Policy OID

- Certificate attribute: Policy OID
- Policy OID: `1.3.6.1.4.1.311.20.2.2`
- Authentication strength: Mutli-factor authentication
- Affinity binding: Low

### Username Binding

| Certificate field | Affinity binding | User attribute    |
| ----------------- | ---------------- | ----------------- |
| PrincipalName     | Low              | userPrincipalName |
| RFC822Name        | Low              | userPrincipalName |

[Microsoft Docs – Certificate-Based Authentication](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-certificate-based-authentication#step-4-configure-username-binding-policy)

## ✅ Wrapping Up

You've now built a secure, scalable two-tier Certificate Authority using Easy-RSA:

- A Root CA that remains offline for maximum trust
- An Intermediate CA that handles day-to-day certificate issuance
- End-entity certificates for services like Microsoft 365 with UPN-based binding
- Exportable PFX files and compatibility with various systems including macOS

This setup not only strengthens your internal security posture but also gives you complete control over your certificate lifecycle, policies, and trust relationships.

## 📘 What's Next?

In the upcoming article, I’ll share how I manage **SSH access for a large number of users and servers**, using a certificate-based and policy-driven approach to simplify operations and tighten security.

If you're building out a lab, managing production systems, or exploring PKI for your org — you're on the right path. Feel free to leave questions or comments below.

Happy cert-ing!
