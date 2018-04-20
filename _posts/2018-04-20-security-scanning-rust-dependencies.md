---
layout: post
title: "Security Scanning Rust Dependencies"
description: "Simple setup for CI testing with Rust Dependency Scanning"
category: rust, ci
blog: true
comments: true
---

[Gitlab](https://gitlab.com) has added support for [dependency scanning](https://docs.gitlab.com/ee/user/project/merge_requests/dependency_scanning.html) to their CI system. The recommended method for enabling this support is to enable their CI step that detects the project language and then runs checks based on that. I'm going to propose a better solution!

## Custom Image

[iceyec/cargo-audit](https://hub.docker.com/r/iceyec/cargo-audit/) is a Docker image that I built that enables usage of the `cargo audit` subcommand during CI runs. The Dockerfile looks like:

```Dockerfile
FROM rustdocker/rust:stable

RUN . ~/.cargo/env \
    && cargo install --git https://github.com/ChrisMacNaughton/cargo-audit.git

ENV PATH="/root/.cargo/bin:$PATH"
```

## .gitlab-ci.yaml

Im my [previous post about Gitlab CI with Rust]({% post_url 2018-04-20-rust-gitlab-ci %}) I demonstrated an example `.gitlab-ci.yml` that I use when starting new Rust projects. In addition to that base, I've started adding the following section to my `.gitlab-ci.yaml` files to take advantage of Gitlab's security scanning of dependencies:

```yaml
dependency_scanning:
  image: iceyec/cargo-audit
  allow_failure: true
  tags: []
  before_script: []
  cache: {}
  dependencies: []
  script:
    - cargo audit || true
    - cargo audit --format=json > gl-dependency-scanning-report.json
  artifacts:
    paths: [gl-dependency-scanning-report.json]
```

## Example

Once the `dependency_scanning` section has been added to a project, Gitlab's CI can output security warnings detected in your dependencies directly into the Merge Request status:

<a rel="shadowbox" href="/images/security_scan.png"><img src="/images/security_scan.png" style="margin: 10px;"/></a>