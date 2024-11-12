---
layout: post
title: Generate password with command line in Linux or macOS
date: 2024-11-09 16:16:00-0700
description: If you usually work with command line in Linux or macOS, you can use it to generate password without installing any additional application.
tags: cli password-generating command-line linux macos
categories: utilities
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

Most websites or application require a strong password that contains a mix of letters, numbers and special characters. And to make it more secure, it should be unique and randomly generated for each account.
If you are using Linux or macOS and usually work with command line, you can use it to generate without installing any additional application.

### Using /dev/urandom or /dev/random

This is my favorite way to generate password because `/dev/urandom` and `/dev/random` are available in most Unix-like operating systems.

```sh
< /dev/urandom tr -dc '[:graph:]' | head -c32; echo
```

This command will create a random password with printable character, length is 32.

In case that alphabet & numeric character, use `alnum` instead of `graph`.

For more information, see `tr` [manpage](https://linuxcommand.org/lc3_man_pages/tr1.html)

With macOS, an error occur when run above command because of UTF-8. Just add LC_ALL=C as below:

```sh
< /dev/urandom LC_ALL=C tr -dc '[:graph:]' | head -c32; echo
```

For more security, I suggest using /dev/urandom because it is really a random value, but in case that ...

### Using openssl

```sh
openssl rand -base64 32
```

This command can only generate a base64 string password.

### Using date and hashing

```sh
date | md5
```

It similar to openssl command, but it's not really random like openssl or /dev/urandom.

### Using gpg

```sh
gpg --gen-random 1 32
```
