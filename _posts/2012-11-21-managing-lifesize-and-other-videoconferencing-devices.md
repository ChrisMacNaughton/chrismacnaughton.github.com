---
layout: post
title: "Managing Videoconferencing Devices"
description: "Managing video conference devices sucks"
category: Videoconferencing
tags: [SSH, videoconferencing]
blog: true
---

I've spent the last couple of months building an interface to replace Lifesize (and Avaya's) video conference management software because their software relies on Flash, is prohibitively expensive, and has a 120 page deployment guide.

I wanted something that would do the same thing without all of the hassle of using outdated, inefficient software.

##The birth of [ControlVC](http://control.vc)

I started by developing a simple application that would connect to devices via SSH and allow me to manage the devices that the company I work at owns.  After spending a couple of weeks improving the interface and the reliability of the connection, I showed a Lifesize distributor what I had done so far.

She requested that I expand the software so that it was usable by others.  So I did.

[ControlVC](http://control.vc) is a hosted alternative to [Lifesize Control](http://www.lifesize.com/en/products/video-conferencing-infrastructure/management-software) that will soon be able to handle scheduling calls and software updates in addition to updating settings, monitoring uptime and call statistics, and even notifying users when their devices trigger various alarms!

##Technology Used

ControlVC is the first place I've ever used workers in addition to a main site and it has been a phenomenal experience.  While I have enough data on the front end to connect to various devices, I decided that the time to initialize an SSH connection to each device and to issue various commands was too long to use within the app.

ControlVC is a full PHP application written from the ground up to scale on Amazons cloud and be able to handle thousands of devices simultaneously.  The system is designed to push updates to devices and retrieve data from devices at an interval and allows me to manage devices by scaling horizontally (distributed workers).

##Making Things Fast

One of the most difficult things about managing video conference devices over SSH is the communication time.

1. Connection: 2 seconds
2. Every command: ~1 second

Connecting to the device, retrieving the name and system version can take 8 seconds.  There are ~143 different settings available on a Lifesize device.

Fortunately, I've managed to retrieve every setting and even push changes within a 10 second window by using every feature that I can to make requests in parallel to devices.

If you're interested in elarning more about ControlVC, [send me an email](mailto:chris@control.vc)