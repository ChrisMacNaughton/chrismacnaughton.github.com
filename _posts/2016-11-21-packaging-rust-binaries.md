---
layout: single
title: "Packaging Rust Binaries"
description: How to build Rust binaries for multiple architectures for Ubuntu
tags: [rust, packaging, ubuntu]
category: rust
blog: true
comments: true
redirect_from: /blog/2016/11/21/packaging-rust-binaries/
---

I spent a bit of time recently working on getting [Launchpad](https://launchpad.net) to build my [Rust](https://rust-lang.org) binary into something that could be easily installed. To make this search easier, I made a minimal rust project that was basically 'Hello World', but using the log crate; I called the project rust-hello. After considering what my goals were, I knew that I had a couple of options:

1. I could build a PPA
  - `apt install rust-hello` after adding my PPA
2. I could use Canonical's new cross-distro packaging system [Snappy](https://snapcraft.io)
  - `snap install rust-hello` immediately after build

Building a source package on Launchpad for a PPA means packaging dependencies into additional PPAs, and then depending on those, up to the root of the rustc dependency. This would mean that, for a crate merely requiring the log crate, I'd have to depend on the rustc available in the repositories (version 1.6.0) and manage my own log crate package (as well as its dependencies, recursively) before I can build my own package.

Snappy, on the other hand, meant adding a `snapcraft.yaml` file to my repository and typing `snapcraft cleanbuild`. The snapcraft.yaml is a relatively simple file for a Rust project:

~~~yaml
name: rust-hello # you probably want to 'snapcraft register <name>'
version: '0.1' # just for humans, typically '1.2+git' or '1.3.2'
summary: Prove cross platform rust snaps # 79 char long summary
description: |
  This is a snap to validate a Rust project building cross architecture
grade: stable # must be 'stable' to release into candidate/stable channels
confinement: strict # use 'strict' once you have the right plugs and slots

parts:
  hello-rust:
    # See 'snapcraft plugins'
    plugin: x-rust
    source: .
apps:
  hello:
command: hello
~~~

Snappy is supported on Launchpad, which means that I was able to build a snappy package on Launchpad with specific hardware targets following the guide at [kyrofa's blog](https://kyrofa.com/posts/building-your-snap-on-device-there-s-a-better-way).

Initially, this failed because Launchpad builders for snappy don't have internet access during the build phase, only during the pull phase, which meant that I had to make a few modifications to add a custom plugin. At the same time, I've opened a Pull Request against Snapcraft to [make the rust plugin work on Launchpad](https://github.com/snapcore/snapcraft/pull/908). This let me build `rust-hello` for all of the Ubuntu supported architectures with a `git push` :-D

If you want to see if it worked and you're on Xenial (16.04) or newer, you can run `snap search rust-hello` to see it, or run `sudo snap install rust-hello` to install the snap. After installing, rust-hello will be available at `/snap/bin/rust-hello.hello` and can be run directly. To make your app available at `/snap/bin/$NAME`, you should make sure that the app_name and snap name defined in snapcraft.yaml match. For example:

~~~yaml
name: rust-hello # you probably want to 'snapcraft register <name>'
...
apps:
  rust-hello:
    command: hello
~~~

The code used to build this snap is available on [Launchpad](https://code.launchpad.net/~chris.macnaughton/rust-hello-snap/+git/rust-hello-snap) or [Github](https://github.com/ChrisMacNaughton/rust_hello).
