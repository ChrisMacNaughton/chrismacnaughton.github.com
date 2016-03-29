---
title: Web Crawling and Data Mining with Apache Nutch
layout: post
date: 2014-01-22 15:39
category: Apache Nutch
tags: [Nutch, Solr, Zookeeper]
blog: true
comments: true
---

[PacktPub][] recently released [Web Crawling and Data Mining with Apache Nutch][nutch-book] and asked that I review the book. I was excited because I've found the Nutch documentation to be spotty and difficult to navigate and hoped that I would learn something new or be able to share a better resource for learning Nutch than digging around the documentation and mailing lists provide.

[PacktPub]: http://www.packtpub.com/
[nutch-book]: http://www.packtpub.com/web-crawling-and-data-mining-with-apache-nutch/book

## Initial Reaction

After finishing Web Crawling and Data mining with Apache Nutch, I can't help but feel like less than half of the book was actually about Apache Nutch. While I accept that talking about how Nutch stores its crawl data is necessary, do we really need an introduction on how to install MySql and Apache Acumulo? It is even less compelling when most of the part about installing Acumulo is copied directly from the [referenced blog post](http://www.covert.io/post/18414889381/accumulo-nutch-and-gora).

The authors have, however, gone through the trouble of compiling information scattered through the documentation and various blog posts into one book. I would like it if the book were better organized though. It feels jumpy, repetitive, and unstructured.

## Structure

It jumps back and forth between Nutch 1.x and Nutch 2.x, often without mentioning which version they are talking about. It would probably have made more sense for the authors to split it into 2 books, one dedicated to each version that try to mash them together so haphazardly. In addition to their propensity for changing versions without warning, the examples regularly use different versions of software for different things. For example, the first section of the book touches on installing [Apache Solr][solr] with version 3.6 but the second section, a more in depth discussion of Solr, starts off with Solr 4.3.

[solr]: https://lucene.apache.org/solr/

Sometimes, it seems that the authors formatted the Nutch documentation instead of writing their own content. In a later section about setting up Zookeeper, the authors exactly copy / pasted the commands out of a referenced blog post instead of even making it fit with the rest of the book's file structures (the path /blogpost/ is in the command still).

## Content

As the authors get into the discussion of Solr, they suggest deploying via an application server such as Tomcat and detail the process required to do that; however, in the next chapter about Sharding with Solr, they go back to using Solr's embedded Jetty and embedded [Zookeeper][] to setup Solr Cloud instead of continuing their explanation in the context of a discreet application server. Once again, this looks like they are copy / pasting the Solr tutorials on getting started with Solr.

[Zookeeper]: https://zookeeper.apache.org/

I get the feeling that the authors felt like they did not have a long enough book so they decided to repeat themselves a lot. They talk about what you will learn in the upcoming chapter, they talk about it in the chapter, they review it at the end of the chapter, and then they remind you that they talked about it in following chapters!

As we progress into talking about Hadoop, the author describes the cluster they are going to demonstrate as having six systems, one master node and 5 slaves. After we get through the initial master configuration we never even touch on how to setup the slaves except for a note that you will need a system like [Chef][] or distributed SSH to manage the many nodes. While the book claims that it will help you integrate Nutch with Hadoop, it only ever touches on Nutch 1.6 with Hadoop and never even mentions Nutch 2.x

[Chef]: http://www.getchef.com/

## At the End

Overall, [Web Crawling and Data Mining with Apache Nutch][nutch-book] feels like it lacks focus. The title states that it's about [Apache Nutch][nutch] but a large portion of the book shows you features of Apache Solr, installing Hadoop, installing Acumulo, installing MySql, setting up [Apache Gora][], and even installing [Eclipse][]!.

[nutch]: https://nutch.apache.org/
[Apache Gora]: https://gora.apache.org
[Eclipse]: http://www.eclipse.org/