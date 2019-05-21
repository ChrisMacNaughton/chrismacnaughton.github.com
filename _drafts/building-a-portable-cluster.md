---
layout: single
title: "Building a Portable Cluster"
description: "Adventures, Successes and Failures in building a portable cluster."
tags: [homelab, diy]
category: homelab
blog: true
comments: true
---

## The Goal

I've had homelabs in various scales from a single computer that's always on to a 42U rack (only partially populated!), but I've never really had anything that was portable so I decided that I wanted to try to design and build a portable lab with plenty of compute and networking. To that end, I choose to harvest my existing home lab and turn it into a portable (ish) compute cluster!

## Hardware

### Management

I had an Intel NUC5i5MYHE available, so I'm planning to build it into the cluster as a management machine. This node will run MAAS and have interfaces (via VLANs) into an OAM network, a public network, and the management network. The management network will include access to the switch's management interface.

### Networking

For my networking, I'm using an Arista DCS-7050T-36, which is a fairly powerful 10G switch with 36 copper 10G ports. This is more than enough 10G networking for my servers (2x10G each) with plenty of networking ability to help manage all of the networking magic that my cluster will need.

### Servers

For the actual servers, I have 4 x [Supermicro E200-8D][servers]. These machines have an embedded Xeon and 32GB of ECC memory each. IN addition, they have two 10GBase-T copper ports, two gigabit ports, and a dedicated IPMI port.

These servers are slightly smaller than 1U in height, and less than half of a standard rack width.

[servers]: https://www.supermicro.com/products/system/Mini-ITX/SYS-E200-8D.cfm