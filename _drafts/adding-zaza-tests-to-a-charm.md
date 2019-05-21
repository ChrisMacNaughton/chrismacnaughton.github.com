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

## Where we came from

## Reading a basic Amulet test

## Writing a Zaza `tests.yaml`

## Don't loose functional coverage!