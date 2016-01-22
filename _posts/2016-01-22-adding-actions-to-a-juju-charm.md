---
layout: post
title: "Adding Actions to a Juju Charm"
description: "How I added simple actions to a Juju charm and tested it"
category: juju
blog: true
---
# Adding Actions to a Juju Charm

I wanted to add a couple of actions to a [Juju][] charm, specifically [Ceph][], and I wanted to be able to test the action to ensure that Ceph received the command and modified its settings.

[Juju]: https://jujucharms.com/
[Ceph]: https://jujucharms.com/ceph/

## Adding an Action

Adding an action to a charm that currently does not have any actions requires two things, the new action to be written and an `actions.yaml` be added to the charm's root directory to describe the new action as well as any options that it takes.

An action can be as sinple as a bash one liner or as complex as a compiled binary, just like a standard [Juju hook][hook]. I went about adding my two hooks, pause and resume, as simple bash files that tell ceph to pause and resume the Ceph cluster's health monitoring.

[hook]: https://jujucharms.com/docs/stable/authors-charm-hooks

### Pause

{% highlight bash %}
#!/bin/bash

set -eux

ceph osd set nodown
ceph osd set noout
{% endhilight %}

### Resume

{% highlight bash %}
#!/bin/bash

set -eux

ceph osd unset nodown
ceph osd unset noout
{% endhilight %}

After adding in my two actions' executables, I created `actions.yaml`:

{% highlight yaml %}
pause:
  description: Pause ceph health operations
resume:
  description: Resume ceph health operations
{% endhilight %}

At this point, I could deploy the Ceph charm as well as run these actions on the cluster. My work was done, right? Wrong! I needed to have tests validating that these actions work; I'd hate to have somebody accidentally break these features at a later date and not be alerted to the break by their tests!

## Testing

Adding a new test to an Open Stack charm seems pretty reasonable, add a function into the right place and it'll run:

{% highlight python %}
def test_402_pause_resume_actions(self):
    """Veryfy that pause/resume works"""
    u.log.debug("Testing pause")
    cmd = "ceph -s"

    sentry_unit = self.ceph0_sentry
{% endhilight %}

Now I've got a running test but how do I actually run and verify the actions? After a lot of digging around, I found another Open Stack charm that does test their actions, [Swift-proxy][], that I could model my test on.

[Swift-proxy]: https://jujucharms.com/swift-proxy/

First, a bit of background. The Open Stack charms tend to test a lot of the Open Stack deployment to verify each service. They each define `u = OpenStackAmuletUtils(DEBUG)` in the setup of the tests. `u` gives a lot of support methods for the Open Stack charms, including logging and authenticating to the various Open Stack services.

Another thing that the `u` offers is the ability to run actions :smile: The code I needed was:

{% highlight python %}
action_id = u.run_action(sentry_unit, 'pause')
assert u.wait_on_action(action_id), "Pause action failed."
{% endhilight %}

After doing this, the action had been run on the first Ceph unit in the cluster. After running the hook, I could add:

{% highlight python %}
output, code = sentry_unit.run(cmd)
import re
if re.match('flags nodown,noout', output) is None:
    amulet.raise_status(amulet.FAIL, msg="Missing noout,nodown")
{% endhilight %}

to run `ceph -s` on the node and check for the expected flags. Testing the resume action is nearly the same, and the whole function is posted below for clarity:

{% highlight python %}
def test_402_pause_resume_actions(self):
    """Veryfy that pause/resume works"""
    u.log.debug("Testing pause")
    cmd = "ceph -s"

    sentry_unit = self.ceph0_sentry
    # action_id = self.action_do(sentry_unit, 'pause')
    action_id = u.run_action(sentry_unit, 'pause')
    # self.action_fetch(action_id)
    assert u.wait_on_action(action_id), "Pause action failed."

    output, code = sentry_unit.run(cmd)
    import re
    if re.match('flags nodown,noout', output) is None:
        amulet.raise_status(amulet.FAIL, msg="Missing noout,nodown")

    u.log.debug("Testing resume")
    action_id = u.run_action(sentry_unit, 'resume')
    assert u.wait_on_action(action_id), "Resume action failed."
    output, code = sentry_unit.run(cmd)
    if re.match('flags nodown,noout', output) is not None:
        amulet.raise_status(amulet.FAIL, msg="Still has noout,nodown")
{% endhilight %}