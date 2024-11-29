---
layout: post
title: P2P - UDP Hole Punching
date: 2024-11-29 11:59:00+0700
description: UDP hole punching is a technique for establishing bidirectional UDP connections between two networked devices behind NATs.
tags: p2p, nat, stun, ice, network
categories: networking
citation: true
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

### Introduction

UDP hole punching is a technique for establishing bidirectional UDP connections between two networked devices behind NATs.

But this technique is not always applicable because of multiple types of NATs.

In this article, we will discuss the UDP hole punching concept and how it works.

### What is NAT?

Because of the limited availability of IPv4, Network Address Translation (NAT) was introduced to allow multiple devices to share a single public IP address.

NAT remaps the private IP:port of devices in private networks to a public IP:port.

There are multiple types of NATs, but for simplicity, we can categorize them into 2 types: Symmetric and anything else.

- Symmetric NAT remaps the public port depending on the endpoint.
- Other types of NATs are independent.

### How does UDP hole punching work?

This technique is originally to solve the firewall problem, because this problem is about NAT & firewall. So we assume that the NAT is include firewall.

Not as TCP, UDP is connectionless and doesn't have a handshake mechanism. So when we send a packet out, it doesn't care that it will reach the destination or not, and any packet coming back is considered as a response.

Assume that we have known public IP:port of A and B.

Idea of UDP hole punching is tricking NATs to allow incoming packets from the destination. There are 2 step to archive this:

- A sends a packet to B (this packet will be lost because B is also behind NAT)
- B sends a packet to A, because NAT considers this as a response, packet will be allowed to pass

But problem of NAT is we don't know the public port of A and B. So we need a third party to help us, this is a device at the public side can see both public side of A and B. It's called STUN server.

STUN server listens on a well-known port and when A and B want to establish a connection, they send a request to STUN server to get their public IP:port.

Then A and B exchange their public IP:port and send a packet to each other to establish a connection.

Because of endpoint independent, non-symmetric NAT always remaps the private IP:port to a fixed public IP:port, the port they get from STUN when sending request is the same when sending packet to each other.

Let do a simple example with netcat:

Server S's IP: _14.123.124.125_

1. Server S listening on 3478/UDP (act as a STUN server)

```sh
nc -luvn 3478
```

2. Client A send a request to S on 3478/UDP

```sh
nc -vu -p 50001 14.123.124.125 3478
```

We can see the source IP:port is **_11.100.101.102:23241_**

3. Back to step 1
4. Client B send a request to S on 3478/UDP

```sh
nc -vu -p 50002 14.123.124.125 3478
```

We can see the source IP:port is **_12.100.101.201:37261_**

Now we have known public IP:port of A and B

Establish the P2P connection:

1. A send a request to B on **_37261_**

```sh
nc -vu -p 50001 12.100.101.201 37261
```

Nothing happened

2. B send a request to A on **_23241_**

```sh
nc -vu -p 50002 12.100.101.201 23241
```

Now A and B have established a connection. Type something on A, we can see it on B and vice versa.

But this just solve the non-symmetric NAT, for symmetric which we cannot predict the public port, we need a relay server call TURN.

This is the last resort when STUN technique doesn't work.

### STUN, TURN and ICE

Interactive Connectivity Establishment (ICE) is a standard for using STUN and TURN to provide a solution for NAT traversal.

They are used in WebRTC, VoIP and many P2P applications.

Mesh VPN is a good example of using ICE to establish a P2P connection.

### Conclusion

In this article, we have discussed the concept of UDP hole punching and how it works, and what is STUN, TURN, ICE and how they resolve the NAT traversal problem.

In case you have any questions, feel free to ask or share your opinion in the comment section.

If you care about my VPN setup with Hub and Spoke, read [this article](/blog/2024/hub-spoke-vpn/)
