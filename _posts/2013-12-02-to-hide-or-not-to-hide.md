---
title: To Hide or Not To Hide
layout: single
date: 2013-12-02 09:32
blog: true
comments: true
redirect_from: /blog/2013/12/02/to-hide-or-not-to-hide/
---

## Why Use a Hidden Service?

While building [TorSearch][], I have encountered some interesting reasons why [Hidden Service][tor-hs] owners would create and run a hidden service. The primary argument I've seen for running a web service as a hidden service is so that the owner can maintain anonymity. Given my tendancy to talk about TorSearch, I'm clearly not doing it for the anonymity.

[TorSearch]: https://kpvz7ki2v5agwt35.onion.to
[tor-hs]: https://www.torproject.org/docs/hidden-services.html.en

What other reasons could there be for running a service as a hidden service? Personally, I'm taking advantage of Tor's hidden service architecture to allow Tor users to search Tor without their traffic leaving the Tor network. Additionally, by only supporting Tor to connect, I gain the encryption that Tor hidden services use without having to setup and use SSL certificates.

### Challenges

The decision to provide access to TorSearch through a hidden service has brought its own set of challenges. One of my largest challenges has been how to properly setup a highly available web service. With a traditional web service, I coud setup round-robin DNS or even broadcast an IP address from several different data centers to ensure availability even in the face of some trouble.

#### Availability

With a Tor hidden service, I cannot get around needing to run a single instance of Tor on a single internet connection to broadcast my hidden service descriptors. The best solution that I have found for handling hidden service load is to have a server running Tor and a proxy server, in my case Nginx, that distributes load to N backend application servers.

While I would like to have multiple instances of Tor to distribute the incoming load, the Tor service has been fairly reliable and capable of serving plenty of connections. Nginx also has had no issues handling the requests but it is possible that one of these pieces may have issues if the load gets high enough.

#### Latency

Running a Tor hidden service comes with an undeniable cost in network latency that isn't present for most web applications. While my appplication can serve non-Tor requests in under 1 second, the average request to my application finished in around 6.5 seconds due to the network time.

Because of the high network time, I have to consider asset size dramatically more than more modern web application do because the Tor network tends to serve hidden services at something barely more than dial-up.

#### Marketing

How do you market something that a large protion of the people who would pay for your service can't access (without extra work)? Considering that I am trying to make TorSearch profitable for me by selling pay per click advertising, I need for companies that are interested in advertising to be able to access my advertiser interface; therefor, I am finally announcing the new [TorSearch.es][new-ts-domain]!

[new-ts-domain]: https://torsearch.es

Let me know your thoughts in the comments!