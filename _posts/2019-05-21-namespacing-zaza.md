---
layout: single
title: "Namespacing Zaza"
description: "Reorganizing Zaza to improve testing experiences!"
tags: [testing, zaza, juju charms]
category: zaza
blog: true
comments: true
---

TLDR: Almost[^1] all of the actual charm tests have been removed from Zaza and moved to a new package in the openstack-charmers namespace: [zaza-openstack-tests]. This new package is named `zaza.openstack` for reasons which I will discuss below!

[zaza-openstack-tests]: https://github.com/openstack-charmers/zaza-openstack-tests.git

Historically, [Zaza] has contained a fairly strong legacy showing it's roots in the OpenStack Charms team, as we developed a testing framework to fit our needs. As Zaza has matured, it has become possible for it to be much more broadly useful; however, the OpenStack legacy means that there are a lot of [OpenStack specific bits in the core library][OpenStack]! For some time now, it has been one of my goals to take advantage of features that Zaza has grown to split the test framework and the OpenStack Charms test implementations into separate libraries, and have recently done most of that work!

[Zaza]: https://github.com/openstack-charmers/zaza.git
[OpenStack]: https://github.com/openstack-charmers/zaza/tree/d4faa3e30ade9d8285aad8e132045417d85a5c9d/zaza/charm_tests

## Why is the new package `zaza.openstack`?

As a part of this work to split out the OpenStack specific tests, we decided to remodel Zaza with a nice namespacing. There are now only a couple of root targets in the Zaza package, `zaza.charm_lifecycle`, `zaza.charm_tests`, `zaza.utilities`, `zaza.controller`, and `zaza.model`. This core package should stay fairly stable at this point.

What this reworking allows, if they wish, is for other projects to namespace their tests inside the `zaza` namespace as well. For example, I could write an Elk Stack series of tests, which may be generally useful to the different components. I could then create my `zaza.elk` package, and embed all of my tests in there! This would allow you to share your common tests as a normal Python package and reference your tests in the `tests.yaml` within the zaza namespace, such as:

~~~yaml
tests:
  - zaza.elk.validate_elasticsearch_is_listening
  - zaza.elk.test_read_write_elasticsearch
configure:
  - zaza.elk.configure_elasticsearch
~~~

Of course, this functionality is not meant to restrict test authors in any way! If you would rather write your tests into a package named `my_awesome_elk_tests`, and reference the above tests as `my_awesome_elk_tests.test_read_write_elasticsearch`, be my guest!

## How can I use the OpenStack bits in my tests?

If you are already using some of the OpenStack charm tests or configuration bits in your Zaza tests, there will be a couple of changes you'll need to make!

First, you'll need to ensure that you pull in the new `zaza.openstack` package in your virtualenv. An example `test-requirements.txt` that we use in the openstack-charms:

~~~diff
git+https://github.com/openstack-charmers/zaza.git#egg=zaza
+ git+https://github.com/openstack-charmers/zaza-openstack-charms.git#egg=zaza.openstack
~~~

Additionally, you'll need to make some changes to your `tests.yaml`:

~~~diff
configure:
- - zaza.charm_tests.glance.setup.add_lts_image
+ - zaza.openstack.charm_tests.glance.setup.add_lts_image
- - zaza.charm_tests.nova.setup.create_flavors
+ - zaza.openstack.charm_tests.nova.setup.create_flavors
tests:
- - zaza.charm_tests.ceph.tests.CephTest
+ - zaza.openstack.charm_tests.ceph.tests.CephTest
~~~

## How should I write my own library?

If you want to start writing your own shared Zaza library, I have a few suggestions from structuring `zaza.openstack`! First, a basic look at the suggested directory structure of a Zaza library:

~~~tree
├── copyright
├── doc
│   └── source
│       ├── generic-utils.rst
├── LICENSE
├── README.md
├── requirements.txt
├── setup.cfg
├── setup.py
├── tox.ini
├── unit_tests
│   ├── __init__.py
│   ├── utilities
│   │   ├── __init__.py
│   │   ├── test_zaza_utilities_generic.py
│   └── utils.py
└── zaza
    ├── __init__.py
    ├── my_package
    │   ├── charm_tests
    │   │   ├── my_service
    │   │   │   ├── configure.py
    │   │   │   ├── __init__.py
    │   │   │   ├── test.py
    │   │   │   └── tests.py
    │   │   ├── test_utils.py
    │   ├── configure
    │   │   ├── my_thing.py
    │   │   ├── __init__.py
    │   ├── __init__.py
    │   └── utilities
    │       ├── generic.py
    │       ├── __init__.py

~~~

Most of these will be fairly self-explanatory as a python package, the important bits are the `zaza/my_package` directory, as that is where your test code will live! As a fan of all kinds of testing, I also suggest including as much unit testing of your code as possible. If you have shared utilities, add tests for those! In the above tree directory, `zaza.my_package.utilities` would be your shared helper functions, not intended to be run by Zaza.

If you want to see examples of what the files listed above contain, a good place to look at is [zaza.openstack][zaza-openstack-tests]


[^1]: Almost, because we made the decision that there may be shared, generic tests that could live in Zaza. Currently, the only included test / configure step is the `NoOp` ones, allowing functional validation of a Zaza test configuration, as is used in the Zaza functional test gate.