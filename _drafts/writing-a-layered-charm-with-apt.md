---
layout: post
title: "Writing a Layered Charm with Apt"
description: "How to write a charm using layers and, more specifically, the apt layer"
categories: juju
blog: true
comments: true
---

Assuming that you want to create a new [Juju charm][jujucharm], you have a couple of options. Assuming you have the most current `charm` installed ([devel][charm-dev]), you can just `charm create demo`. Calling this will generate several things for you:

[jujucharm]: https://jujucharms.com
[charm-dev]: https://launchpad.net/~juju/+archive/ubuntu/devel

~~~bash
demo
├── config.yaml
├── icon.svg
├── layer.yaml
├── metadata.yaml
├── reactive
│   └── demo.py
├── README.ex
└── tests
    ├── 00-setup
    └── 10-deploy
~~~

Right now, the most interesting files to us are `layer.yaml` and `reactive/demo.py`.

Layer.yaml is a fairly brief file:

~~~yaml
includes: ['layer:basic']  # if you use any interfaces, add them here
~~~

