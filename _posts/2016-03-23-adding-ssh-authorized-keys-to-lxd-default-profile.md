---
layout: post
title: "Adding Authorized Keys to the LXD Default Profile"
description: "How to add your SSH keys to LXC instances you create by default"
categories: lxd, linux
blog: true
comments: true
---

Adding your key is as simple as:

```bash
$ lxc profile edit default
```

You will then need to modify the contents from:

{% highlight yaml %}
config: {}
{% endhighlight %}

to:

{% highlight yaml %}

config:
  user.user-data: |
    #cloud-config
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCccE2pTHvz97SNVB6GssYbcAPhNkYVxnxD+HAa0IXx+/RaqnMyqnBToJVS1ZY7tNlzGmQ0zF1o8sVWnbkBq9uPFzsUfIKkOxqwSbQmfNIDE3p+g3lyGd1qUAbb6nJzCV4edT77AuZ/JDa9V6UtB90WsP8FJ1N87KI5KMYVKWSupmG0GTfS748sMFz7h9csxWOeC2wYEV77D4+T/bYVIpHK6RVCBj0sumJ4BIjSlmgxb3UzRU6O9Ah1ApoICt2yL7Lgbe/fNqZVnF84Dv1mz+CHiW6BfNCQbgcx/o9uFqtW+ssLxkQK3Sh51AqrtoG7o274K4rJDpVYNZTvOe3SRzJu8WtrxxRjjX64uJ7rNB41aJ5doiAnbeYcHXc4WA3w2OZDAZJdg9QiLOzxjNxPDlHf2Cg398sRomqjGn3lmrw40qIhILzmP4M2b8DxDH2iA3aNNQmE2HK6EFIlfz/XN20sQmXCzRVMpor6RzxczlvcIixqrNxJxYL4GCSHy5eKDMO4J4sYLR3CwCe2xgbVDGigGLbPedof2EarnDybffK8U+vneSpIgDcERpcESO0Wh42Hp4avGIHhZrhxhg7ZuwFSE/gLx0Swi3rki90ANWQ1uuFxfFe01cORV8qyDG+XoDYvnU2ko7lr7BwjqFH+FfDo0Y4ft64BAsKP5nXLRyAHaw== ssh key
{% endhighlight %}

Where the ssh_authorized_key is _your_ public key, probably identified by `cat ~/.ssh/id_rsa.pub`.