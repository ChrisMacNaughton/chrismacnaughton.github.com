---
layout: post
title: "What Can You Do for Privacy?"
description: ""
category: Privacy
tags: [encryption, privacy]
blog: true
---
{% include JB/setup %}

<a rel="shadowbox" href="/assets/images/privacy.jpg"><img src="/assets/images/privacy.jpg" width="25%" height="25%" style="float: right; margin: 10px;"/></a>

A friend of mine posted this image on Facebook today and it got me to thinking, 'What are some fairly easy things that people can use to keep their data private that I could share?' After thinking about it for a bit, I've come up with a few simle things that you can do to keep personal data private!

## Use [GnuPG][gnupg]

Using GnuPG really doesn't have to be hard, there are plugins for all of the major email clients that allow automatic <a href="#signing">signing</a> (authenticating that a message did in fact come from you) and <a href="#encrypting">encrypting</a> (making it impossible* for unauthorized people to read your email) automatically.

### Windows

If you're using Windows, you can grab a program like <a href="gpg4win.org">GPG4Win</a> which has a <a href="#key-manager">key manager</a> and plugin for Outlook 2003 and 2007. If you're using <a href="https://www.mozilla.org/en/thunderbird">Thunderbird</a> instead of Outlook for your primary email client, you should install the plugin Enigmail to handle PGP signing and encrypting.

### Mac

  If you're using Mac, <a href="https://gpgtools.org">GPGTools</a> is a full installer to <a href="#key-manager">managing GPG keys</a> through signing and does also include a plugin for Mail.app. Once again, if you're using <a href="https://www.mozilla.org/en/thunderbird">Thunderbird</a>, Enigmail is a great plugin!

### Linux

  If you're on Linux, [GnuPG][gnupg] should be installed by default but you may need to use your package manager to install it (```apt-get install gpg``` or ```yum install gpg```) if it isn't. My preferred <a href="#key-manager">key manager</a> for Linux is <a href="https://projects.gnome.org/seahorse/">Seahorse</a> which is a Gnome package but can be installed in other environments, it only depends on a coupld of Gnome packages.

<p class="muted"><small>*impossible barring quantum computers, 100 years of Moore's law, or better algorithms.</small></p>

If you want a more in depth article, check out [Futureboy!](http://futureboy.us/pgp.html)

<a name='signing'><h1>Signing</h1></a>

## Message Signing

<img src="/assets/images/signing.jpg" width="25%" height="25%" style="float: left; margin: 10px;"/>

Signing a document is done by generating a compact, encrypted version of the content you want to sign. After a document is signed, it can be verified that it was signed by the signer's private key with their public key and thusly verify that they did, in fact sign it.

As with everything depending on private keys, if your private key is compromised, your signatures nor your encryption can be trusted so make sure to keep that key private!

## Key Signing

In addition to signing a message to validate that it did come from you, can you also sign somebody else's public key, thereby telling the world that you believe that the signed key belongs to the person using it and that the person using it is not mis-representing themself!

Key signing is an integral part of GnuPG because it allow you to build a web of trust, you can trust other people which allows you to trust people that they trust without having to verify their identity yourself!

<a name='encrypting'><h1>Encrypting</h1></a>

<img src="/assets/images/locking.jpg" width="25%" height="25%" style="float: left; margin: 10px;"/>

Encrypting a message is generally done in the same way that signing a message is done except that it uses your recipient's public key to generate a binary blob that is entirely unreadable without using their private key to decrypt! GPG encrypted messages are also digitally signed so that you can verify that the sender did, in fact, send you the message.

As long as you have the public key of somebody, you can send them encrypted communication with GnuPG!

<a name='key-manager'><h1>Key Manager</h1></a>

A key manager is a graphical program that provides an interface to manage and create your private and public keys. Some of my favorites are [GPGTools][gpgtools] [Keychain Access][keychain] (Mac), [Kleopatra][gpg4win] (Windows), and [Seahorse][seahorse] (Linux). They all allow generating private keys, singing keys (marking the key to say that you have verified its owner), and validating other people's keys.

<a href="/assets/images/keychain-manager.png" rel="shadowbox"><img src="/assets/images/keychain-manager.png" width="25%" height="25%" style="float: left; margin: 8px; padding: 2px; border: 2px grey solid"/></a>

To the left you can see a screenshot of my key library in [GPGTools][gpgtools]. The bold one is my personal key, it is bold in the client because I have the private key that corresponds to the key. All of the other keys are keys that I have downloaded from keyservers so that I can send email to them or because I wanted to validate their signatures.

[gpgtools]: https://gpgtools.org
[keychain]: https://gpgtools.org/keychain/index.html
[gpg4win]: http://gpg4win.org
[seahorse]: https://projects.gnome.org/seahorse/



[gnupg]: http://www.gnupg.org