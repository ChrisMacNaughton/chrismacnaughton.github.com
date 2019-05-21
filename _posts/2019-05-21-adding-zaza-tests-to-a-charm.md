---
layout: single
title: "Adding Zaza Tests to a Charm"
description: "A basic guide on transforming a set of Amulet tests to a set of Zaza tests"
tags: [testing, zaza, juju charms]
category: zaza
blog: true
comments: true
---

Migrating from Amulet to Zaza can seem like harrowing experience, but it doesn't have to be! In this post, we will take a charm written with Amulet tests and convert it to a Zaza charm. This will be a multiple step process, starting with adding the tests in-tree, and then later migrating them to a shared library. While migrating the tests to a shared library is wonderfully useful if you are writing tests that may be useful to other services, it is an optional step!

Our example will work through the changes we might make to the [Ubuntu charm]

[Ubuntu charm]: https://github.com/juju-solutions/charm-ubuntu

## Where we came from

In the Ubuntu charm's tests directory, we see several things:

~~~bash
$ tree tests
tests
├── 010_basic_precise
├── 015_basic_trusty
├── 030_basic_xenial
├── 035_basic_artful
├── 040_basic_bionic
├── basic_deployment.py
├── charmhelpers
│   ├── contrib
│   │   ├── amulet
│   │   │   ├── deployment.py
│   │   │   ├── __init__.py
│   │   │   └── utils.py
│   │   └── __init__.py
│   └── __init__.py
├── setup
│   └── 00-setup.sh
└── tests.yaml
~~~

## Reading a basic Amulet test

The important things to note in the file tree showing the tests directory in the Ubuntu charm are the structure of one of the `##_basic_series` files, and the basic_deployment.py.

First, we'll see the executable, `040_basic_bionic`:

~~~python
#!/usr/bin/python
"""Amulet tests on a basic ubuntu charm deployment on bionic."""

from basic_deployment import ubuntu_basic_deployment

if __name__ == '__main__':
    ubuntu_basic_deployment(series='bionic')
~~~

The other `##_basic_series` files are almost identical, excepting the `series='bionic'` line

We can see that we're importing the function `ubuntu_basic_deployment` from `basic_deployment`, so lets look at that next:

### basic_deployment.py

First, we have some basic documentation and a couple of imports:

~~~python
#!/usr/bin/python3
"""
Ubuntu charm functional test.  Take note that the Ubuntu
charm does not have any relations or config options
to exercise.
"""

import amulet
from charmhelpers.contrib.amulet.utils import AmuletUtils
import logging
~~~

Now we come to the meat of this test, `ubuntu_basic_deployment`, which starts with some basic Amulet configuration, and a command to deploy the `ubuntu` charm:

~~~python
def ubuntu_basic_deployment(series):
    """ Common test routines to run per-series. """

    # Initialize
    seconds = 900
    u = AmuletUtils(logging.DEBUG)
    d = amulet.Deployment(series=series)
    d.add('ubuntu')
~~~

After we deploy `ubuntu`, we want to ensure that the deployment completed and the units are ready:
~~~python
    # Deploy services, wait for started state.  Fail or skip on timeout.
    try:
        d.setup(timeout=seconds)
        sentry_unit = d.sentry['ubuntu'][0]
    except amulet.helpers.TimeoutError:
        message = 'Deployment timed out ({}s)'.format(seconds)
        amulet.raise_status(amulet.FAIL, msg=message)
    except:
        raise
~~~

Now we're ready to actually run our test! In this test, what we're doing is getting the application version from the deployed unit and ensuring that the series equals the series we were configured to run before!

~~~python
    # Confirm Ubuntu release name from the unit.
    release, ret = u.get_ubuntu_release_from_sentry(sentry_unit)
    if ret:
        # Something went wrong trying to query the unit, or it is an
        # unknown/alien release name based on distro-info validation.
        amulet.raise_status(amulet.FAIL, msg=ret)

    if release == series:
        u.log.info('Release/series check:  OK')
    else:
        msg = 'Release/series check:  FAIL ({} != {})'.format(release, series)
        u.log.error(msg)
        amulet.raise_status(amulet.FAIL, msg=msg)
~~~

In summary, the Amulet tests for the Ubuntu charm deploys a charm on a particular series, waits for the Juju model to stabilize, and then ensures that the Ubuntu charm sets it's application version to the correct value!

## Writing a Zaza test

Lets look at how we could reproduce the above behaviour in Zaza!

First, we'd want a slightly different file structure:

~~~bash
$ tree tests
tests
├── bundles
│   ├── artful.yaml
│   ├── bionic.yaml
│   ├── precise.yaml
│   ├── trusty.yaml
│   └── xenial.yaml
└── tests.yaml
└── basic_deployment.py
~~~

Taking this one step at a time, the `tests.yaml` might look like:

~~~yaml
gate_bundles:
  - precise
  - trusty
  - xenial
  - artful
  - bionic
smoke_bundles:
  - bionic
tests:
  - tests.basic_deployment.BasicDeployment
~~~

Breaking this down a little bit, the `gate_bundles` and `smoke_bundles` keys are used to configure Zaza. When calling Zaza's `functest-run-suite`, by default the `gate_bundles` will be run and tested, but it's possible to pass `--smoke`, at which point only the `smoke_bundles` will be run.

The `tests` key refers th `unittest.TestCase` instances. In this case, I've started by modifying the `basic_deployment.py` into:

~~~python
#!/usr/bin/python3
"""
Ubuntu charm functional test using Zaza. Take note that the Ubuntu
charm does not have any relations or config options to exercise.
"""

import unittest
import zaza.model as model

class BasicDeployment(unittest.TestCase):
    def test_ubuntu_series(self):
        pass
~~~

Given our example using `bionic` earlier, let's look at that `bundles/bionic.yaml` next:

~~~yaml
series: bionic
applications:
  ubuntu:
    charm: cs:ubuntu
    num_units: 1
~~~

This bundle is just a plan [Juju bundle][bundle]. As you can see, we configure the series at the top level and deploy the ubuntu charm.

Given the above configuration, We should have enough to proove that our Zaza setup is workable, although we have no useful tests yet! Just to confirm, I've setup a virtualenv, installed Zaza, and run `functest-run-suite --keep-model --smoke` which results in:

~~~bash
2019-05-21 11:30:02 [INFO] Deploying bundle './tests/bundles/bionic.yaml' on to 'zaza-637dd5f09105' model
Resolving charm: cs:ubuntu
Executing changes:
- upload charm cs:ubuntu-12 for series bionic
- deploy application ubuntu on bionic using cs:ubuntu-12
- add unit ubuntu/0 to new machine 0
Deploy of bundle completed.
2019-05-21 11:30:07 [INFO] Waiting for environment to settle
2019-05-21 11:30:07 [INFO] Waiting for a unit to appear
2019-05-21 11:30:07 [INFO] Waiting for all units to be idle
2019-05-21 11:31:53 [INFO] Checking workload status of ubuntu/0
2019-05-21 11:31:53 [INFO] Checking workload status message of ubuntu/0
2019-05-21 11:31:53 [INFO] ## Running Test tests.basic_deployment.BasicDeployment ##
test_ubuntu_series (tests.basic_deployment.BasicDeployment) ... ok

----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
____________________________________________________________________________________ summary _____________________________________________________________________________________
  func-smoke: commands succeeded
  congratulations :)
~~~

[bundle]: https://old-docs.jujucharms.com/2.5/en/charms-bundles

## Don't loose functional coverage!

Now, we should ensure that we still maintain our existing functional test! This shouldn't be too hard. Let's update our `basic_depoloyment.py` like this:

~~~diff
#!/usr/bin/python3
"""
Ubuntu charm functional test using Zaza. Take note that the Ubuntu
charm does not have any relations or config options to exercise.
"""

import unittest
import zaza.model as model

class BasicDeployment(unittest.TestCase):

    def test_ubuntu_series(self):
-        pass
+        first_unit = model.get_units('ubuntu')[0]
+        result = model.run_on_leader('ubuntu', 'lsb_release -cs')
+        self.assertEqual(result['Code'], '0')
+        self.assertEqual(result['Stdout'].strip(), first_unit.series)
~~~

If we run the same command as above, we should get the same output, but this time, we've actually validated the same thing that the Amulet test validated before, success!

## Summary

In this change, we have accomplished the following changes:

~~~
 test-requirements.txt                           |   1 +
 tests/010_basic_precise                         |   7 --
 tests/015_basic_trusty                          |   7 --
 tests/030_basic_xenial                          |   7 --
 tests/035_basic_artful                          |   7 --
 tests/040_basic_bionic                          |   7 --
 tests/basic_deployment.py                       |  48 +++--------
 tests/bundles/artful.yaml                       |   5 ++
 tests/bundles/bionic.yaml                       |   5 ++
 tests/bundles/precise.yaml                      |   5 ++
 tests/bundles/trusty.yaml                       |   5 ++
 tests/bundles/xenial.yaml                       |   5 ++
 tests/charmhelpers/__init__.py                  |  38 ---------
 tests/charmhelpers/contrib/__init__.py          |  15 ----
 tests/charmhelpers/contrib/amulet/__init__.py   |  15 ----
 tests/charmhelpers/contrib/amulet/deployment.py |  93 ----------------------
 tests/charmhelpers/contrib/amulet/utils.py      | 533 --------------------------------------------------------------------------------------------------------------------------
 tests/setup/00-setup.sh                         |   9 ---
 tests/tests.yaml                                |  22 +++--
 tox.ini                                         |  35 ++++++++
 20 files changed, 81 insertions(+), 788 deletions(-)
~~~

An additional, small, takeaway for the readers who made it all the way to the bottom, an example `tox.ini` that the OpenStack charms project uses for managing out virtualenvs with Zaza:

~~~ini
[tox]
envlist = pep8
skipsdist = True

[testenv]
setenv = VIRTUAL_ENV={envdir}
         PYTHONHASHSEED=0
whitelist_externals = juju
passenv = HOME TERM CS_API_* OS_* AMULET_*
deps = -r{toxinidir}/test-requirements.txt
install_command =
  pip install {opts} {packages}

[testenv:pep8]
basepython = python3
deps=charm-tools
commands = charm-proof

[testenv:func-noop]
basepython = python3
commands =
    true

[testenv:func]
basepython = python3
commands =
    functest-run-suite --keep-model

[testenv:func-smoke]
basepython = python3
commands =
    functest-run-suite --keep-model --smoke

[testenv:venv]
commands = {posargs}
~~~

and a snippet from a `test-requirements.txt`:

~~~
git+https://github.com/openstack-charmers/zaza.git#egg=zaza
~~~