---
layout: post
title: "Choosing the Right Password Hashing Algorithm"
date: 2026-03-25 14:30:00+0700
description: A practical guide to secure password hashing, comparing Argon2id, bcrypt, scrypt, and PBKDF2 to prevent offline brute-force attacks.
mermaid:
  enabled: false
  zoomable: false
tags: cryptography security password-hashing best-practices
categories: security
citation: true
giscus_comments: true
toc:
  beginning: true
---

## Problem with Password Storage

When passwords are stored, they must remain secure even if the database is compromised.

Even if your application has brute-force protection, what happens when an attacker gets the hashes?
They can always brute-force them **offline**.

With older hashing algorithms like MD5 or SHA-1, attackers can compute billions of hashes per second using modern hardware. That makes weak passwords trivial to crack.

So, solution is: **slow down offline attacks** by using hashing algorithms that are intentionally expensive to compute.

Below is a list of commonly recommended password hashing algorithms:

- **Argon2id**: Winner of the 2015 Password Hashing Competition. Best choice for new systems. Strong resistance against both GPU-based attacks and side-channel attacks.
  Minimum recommended parameters: `memory = 19 MiB`, `iterations = 2`, `parallelism = 1`
- **scrypt**: Good alternative with strong memory hardness.
  Minimum configuration: `N = 2^17`, `r = 8`, `p = 1`
- **bcrypt**: Common in legacy systems. Still secure if configured properly.
  Recommended cost factor: `12+`
- **PBKDF2**: Mostly used when FIPS-140 compliance is required.
  Minimum configuration: `iterations = 600,000`, `hash = HMAC-SHA256`

Avoid fast hashing algorithms like MD5, SHA-1, or even SHA-256 for password hashing.
They are designed for speed, which makes them easy to brute-force with modern hardware.

## What is Argon2id?

Argon2id is a variant of Argon2. It’s not just a password hashing algorithm — it’s a **key derivation function (KDF)**.

### What is a Key Derivation Function (KDF)?

A KDF is used to derive secure cryptographic keys from a secret input (like a password).

Purpose:

- Make brute-force attacks slower
- Increase the cost of guessing each password

In practice, a KDF turns a weak input (human password) into something much harder to attack.

## Why Argon2id?

Argon2 has three variants:

- **Argon2d**
  Optimized for resistance against GPU attacks (memory-hard), but vulnerable to side-channel attacks.
- **Argon2i**
  Resistant to side-channel attacks, but weaker against GPU-based brute-force.
- **Argon2id**
  Hybrid approach — combines both. This makes it the most balanced and recommended option.

Besides the security benefits, there are trade-offs:

- More resource-intensive (CPU + memory)
- Slightly more complex to implement correctly

## Conclusion

If you’re building a new system, use **Argon2id** with recommended parameters.

For existing systems, **bcrypt** is still a safe and practical choice.

Avoid fast hashing algorithms like MD5 or SHA-based hashes for passwords.

The goal is not just to hash passwords, but to make brute-force attacks as expensive as possible.
