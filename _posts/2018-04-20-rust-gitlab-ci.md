---
layout: post
title: "Rust CI on Gitlab"
description: "Using Gitlab CI for effective testing of Rust libraries and binaries"
category: rust, ci
blog: true
comments: true
---

## Summary

Setting up Rust CI on Gitlab is a fairly easy task, until you want to actually use it ;-) Throughout my time developing Rust libraries and applications, I've tried over and over to get a good generic CI setup for Rust on Gitlab, and I think that the following example is that!

In addition to running your builds on stable, beta, and nightly, the configuration presented here also runs rustfmt and clippy on the code to help catch style issues and lint your code.

## .gitlab-ci.yml

```yaml
# Setup a cache to cache job parts between jobs to ensure faster builds
cache:
    key: "$CI_JOB_NAME"
    untracked: true
    paths:
    - $HOME/.cargo/
    - target/

# Define a yaml template for running a build and then running your tests
.cargo_test_template: &cargo_test
  script:
  - rustc --version && cargo --version
  - cargo build
  - cargo test --verbose

# Set any required environment variables here
variables:
  RUST_BACKTRACE: "FULL"

# Do any pre-flight requirements here, such as updating $PATH installing dependencies
before_script:
  - export PATH="/root/.cargo/bin:$PATH"

# The following test: stages inherit from the test template above and
# configure the image used for the various Rust release trains
test:stable:
  image: "rustdocker/rust:stable"
  <<: *cargo_test

test:beta:
  image: "rustdocker/rust:beta"
  <<: *cargo_test

test:nightly:
  image: "rustdocker/rust:nightly"
  allow_failure: true
  <<: *cargo_test

# Always want to run rustfmt and clippy against our tests, to ensure that
# we aren't using any anti-patterns or failing to follow our style guide
lint:rustfmt:
  image: "guangie88/rustfmt-clippy"
  # allow_failure: true
  script:
    - cargo fmt -- --write-mode=diff

lint:clippy:
  image: "guangie88/rustfmt-clippy"
  allow_failure: true
  script:
    - cargo clippy -- -D warnings # Turn all warnings into errors
```