---
layout: post
title: "Setting Up a New Vagrant Box"
description: "Setting up a new Vagrant box from scratch, including building a new basebox, can be intimidating!"
category: Dev-Ops
tags: [vagrant, ruby, chef]
blog: true
---
{% include JB/setup %}

[<img src="/assets/images/vagrant-logo.png" width="25%" height="25%" style="float: right; margin: 10px;"/>](http://vagrantup.com)

On Sunday, I decided to build a new Vagrant base box because none of the base boxes that I could find online had a newer version of Ruby (1.9.3) or chef-client (11+) so I finally decided to build a new one form scratch that I could use in testing my new [Bittorrent Sync](http://labs.bittorrent.com/experiments/sync.html) [app](https://github.com/ChrisMacNaughton/BtSync) and the front end I'm working on with [tubbo](http://psychedeli.ca), [BitBox](https://github.com/tubbo/bitbox).

I spent a while looking for guides and documentation regarding what was required to run a new [Vagrant](http://vagrantup.com) box and couldn't find current documentation, everything was out of date!

I decided that I was going to start with Ubuntu 12.04. Some key things to keep in mind with the guest operating system for Vagrant is that you want the primary user to be named vagrant. After going through the regular install for Ubuntu 12.04 Server edition on [VirtualBox](http://virtualbox.org), I installed the Guest Additions by clicking "Install Guest Additions" in the VirtualBox interface. After the cd drive was loaded, I moved to it with ```cd /media/cdrom``` and the ```sudo bash ./VBoxLinux*```. It spent a little while churning away building the Guest Additions and installing them.

After getting the Guest Additions installed, I made sure to add the public key from [Vagrant's Github Repo](https://github.com/mitchellh/vagrant/blob/master/keys/vagrant.pub) to my ```/home/vagrant/.ssh/authorized_keys``` so that Vagrant would be able to connect to and setup a new box. Failing to add the authorized_key will leave the Vagrant box unable to be used!

<a href="/assets/images/ubuntu-ready.png" rel="shadowbox"><img src="/assets/images/ubuntu-ready.png" alt="Ubuntu us installed and ready!" width="25%" height="25%" style="float: left; margin: 10px;" /></a>

Once I had the keys setup, it was time to shut down the Virtual Box and run ```vagrant package --output ubuntu-12.04-updated.box --base "Ubuntu 12.04"``` to export my box named "Ubuntu 12.04" in Virtual Box to a Vagrant box named "ubuntu-12.04-updated.box"!

The link to use for the basebox referenced above is [http://bit.ly/1488yGQ]().

I will work to keep this updated with my experience building Vagrant Base Boxes.