---
layout: post
title: Hub & Spoke VPN
date: 2024-11-29 11:59:00-0400
description: Hub & Spoke VPN is a VPN network topology that connects multiple remote peers (spokes) to a central location (hub) to provide secure communication between them.
tags: vpn, openvpn, server, network, remote, hub, spoke, remote-work, security
categories: networking
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

### Introduction

Hub & Spoke VPN is a VPN network topology that connects multiple remote peers (spokes) to a central location (hub) to provide secure communication between them.

Spokes can only communicate with each other through the hub. The hub is a single point of failure, so if we want a reliable network, we need to increase the availability of the hub.

In this post, we will discuss the Hub & Spoke VPN and how it solve the problem of working activity in the remote area.

### Problem

I'm a software engineer usually working in the office. But sometimes I need to work remotely from home, coffee shop, or other place. And I also want to build a server that can be accessed from anywhere securely.

### What we need

- A secure & manageable network to setup VPN server (hub), so I will use my home network.
- A device acts as a VPN server (hub), it can be an old computer, Raspberry Pi or any device that can run VPN server software.
- One or more VPN clients (spokes), in my case, they are my laptop, smartphone and a personal server, all of them are in other locations and in different private networks.

I will use OpenVPN because it can be installed on many platforms and easy to use. Make sure you have enabled client to client communication.

My hub is a Raspberry Pi running Raspbian OS and iptables (default).

Install OpenVPN client on personal server, laptop and any device you want to join the VPN network.

Connect the VPN clients to the VPN server (hub) using the OpenVPN client software, setup auto connect. Boom! Now you can access your home server or other devices in the VPN network.

In case you want to access the internet though the hub, setup the VPN server to route all traffic though it.

Because I have a server and want to publish my some services to the Internet, I will setup port forwarding (NAT) on the hub to forward the traffic to the server.
To optimize the bandwidth, the server which only listens traffic from the hub, only routing traffic of VPN subnet via tun interface, default is to the Internet.

For some work that I need the office's computer and some resources in the office's network, connect the office's computer to the VPN network then start a proxy server on this computer.

### Use cases

I usually use this setup for:

- Securely Internet access when I'm in public places.
- Access my home server and office's computer from anywhere.
- Communicate with other devices in the VPN network.
- Seamlessly work in the remote area.
- Publish services to the Internet with server at any location.

With me, working almost on the CLI, so with neovim, tmux, ssh and other tools, I can work seamlessly anywhere, any device which have terminal emulator and internet connection.
I call this setup Unified Working Environment.

### Disadvantages

Because hub is a single point of failure, if it goes down, all spokes will lose connection to each other.

All traffic between spokes must go through the hub, so the hub can be a bottleneck. So if you have many spokes and need high bandwidth, you can consider a mesh network.
I suggest Tailscale, it's easy to use and setup and can be used for free.

### Conclusion

In this article, I will show you my hub & spoke VPN setup and how it helps me in my working activities. I hope you can find it useful and apply it to your work.

If you have any questions, suggestions or your own setup, please leave a comment below.
