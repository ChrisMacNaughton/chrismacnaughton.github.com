---
layout: post
title: "The Future of Cyber Security Innovation"
description: "The Future of Cyber Security Innovation"
category: Cybersecurity, Security
tags: security
blog: true
---
{% include JB/setup %}

At an event hosted by the [Christian Science Monitor (Passcode)](http://csmpasscode.com/), [invincea](http://www.invincea.com/), and [Vectra](http://www.vectranetworks.com/), DARPA's Frank Pound gives a live demonstration of Plan X.

## Plan X

#### Frank Pound

Plan X is a DARPA initiative to improve network and device security. Plan X's Mission is to reduce the overhead for detection of threats against a network or the devices that make up a network. It was developed in response to the question of how to provide real-time interfaces for attack detection without massively increasing the requirement for very highly trained individuals in cyber security.

The result is that, with Plan X, the Cyber Operations Platform allows junior analysts and regular soldiers to take part in improving network security visually through a touch interface. The Spirit of Plan X is to make threat visualization, detection, and remediation performable by individuals with less training than a typical security consultant.

### What can it do?

Plan X supports things like filtering devices by MAC address and location, such that, for example, an operator could find devices that were near an improvised explosive device attack a year ago with devices seen near a forward operating base to correlate possible threats to network and physical security. Additionally, Plan X's interface should support quickly, visually demonstrating that an attack is taking place in real-time, and it has been suggested that there may be explosions in the interface to illustrate this. Ideally, Plan X should surface threats automatically, as they happen. Plan X also has a chat interface so that operators could communicate with the Analytic writer for help with correctly deploying the Analytic on their target.

In addition to the core user interface for Plan X, an App Store like system has been developed that allows operators to select already written pieces of LUA code to execute on a machine in the network. This bit of code is called an `analytic` and could be anything from enumerating the processes on a machine to killing a specific piece of software if found, when detecting malware for example. Analytics are run within an execution container on the host, through an agent architecture, so that a host requires the monitoring and execution agent to be pre-installed for this functionality to work.

Plan X represents data about a given device with more details in the form of metadata than is typical for security products. The device may be accompanied with data such as thumb drives that are (or have been) connected to it or what software is installed. This data allows operators to track things such as thumb drives through their activity within a network. One of the key benefits experiences so far is to improve planning cyber missions within Cyber Command by reducing the lead time on a mission from weeks or months down to days.

### How is it made?

The current version of Plan X is the third major revision, and has been built in HTML5 with [D3.js](http://d3js.org) for visualizations. The previous version was built in the Unity game engine but Unity was scrapped to reduce the difficulty of deployment. Despite the fact that the current version has only been under active testing for a couple of months (Since October), the new version is already under active development using open source libraries such as [Ember.js](http://emberjs.com/). Plan X is deployed as a series of Docker containers, such that each service that makes up Plan X (web interface, app store, etc) are individually scalable to improve device utilization.

The network view is a cluster with nodes representing machines attached by their network distance from one another. Additionally, at higher zoom levels, a single node may expand into multiple nodes surrounding a given device containing additional metadata about the particular details or services included with the node.

Plan X is generally a centralized solution; however, it has Plan X Satelites that can be distributed and used remotely with limited internet access and still with with nearly full capability, analogous to a military group deploying with a chest of maps, one may now deploy with a computer and a lot of hard disks of data to deploy a Plan X Satelite. When the Satelite has internet access, it would be able to sync data up and down with the Core to keep data in sync globally.

### Using Plan X

Upon login, an operator is presented with all active missions that they are involved in. Additionally, an operator can create a new mission from this interface. If such a mission requires additional approval from higher up the command chain, that approval can be requested and somebody higher can authorize the mission. When a mission is initiated, Plan X will request the necessary Anaytic to perform the required task from the App Store, requesting one that can kill processes on a Windows 8 machine, for example. An operator, building out a set of tasks to complete on a host or on the entire network, can test their mission within an ad-hoc setup of virtual machines on an Open Stack cluster to validate that their mission will work as expected.

While many of the Analytics can be used directly and automatically, without any user intervention; however, there may be other tasks that might need user intervention or confirmation that it should continue. What this does is pop up an alert to the required user to continue or abort the current task.

One of the largest hurdles for plan X has been the definitions used to describe a network or a given attack. A lot of effort has gone into generating semantically correct vocabulary that can be used to discuss a network element, device, or threat to clearly understand what is being discussed. Additionally, graphic elements have been developed to visually clarify what things are. In addition to using advanced language to specifically display things on screen, a 'Graph Query Language' has been developed for use in creating a mission. 

Plan X can use many tools to ingest data about hosts, from its internally developed agents to tools developed by third parties such as Vectra Networks.

## Vectra Networks

#### Mike Banic

### Security that thinks

Mike Banic is the first speaker and he was discussing detecting cyber attacks as they happen. Mike starts with discussing known breaches in security, such as those against banks and retail stores. Mike headlines with a discussion of what companies do after their data has been exposed: hiring a group to identify the source of the leak: where and how it happened.

While the attack is happening, Mike contends that people don't notice the attack. Combining security in depth (often already deployed) with machine learning so that the active security monitoring can happen in real-time, all the time (Automated Breach Detection).

Vectra supplies software that monitors and detects security risks to improve efficiency of a security team within an organization. Vectra's dashboard claims to make it easy to detect security breaches: one can dig in on a risk with two clicks.

Vectra's dashboard doesn't require breaking SSL to detect Command and Control behavior over HTTPS. Practically, this means detecting HTTPS connections that are managed by an external connection. Additionally, their software will detect port scanning. Vectra's `Data Smuggler` detector identifies data ex-filtration so that the security team can remedy the risks.

The dashboard provides threat / certainty for each host detected to make life easier for security teams.

What do you think? Would you be interested in seeing something like Plan X for you to use in protecting your home and your personal internet connected devices? Share your thoughts below!