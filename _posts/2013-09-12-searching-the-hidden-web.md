---
layout: post
title: "Searching the Hidden Web"
description: "I have built a search engine and learned more about Tor in less time than I believe I could have through normal exploration"
category: Search
tags: [Tor, Ruby on Rails, Apache Solr, Apache Nutch]
blog: true
---

## Crawling the Deep Web

I have spent the past month building an index of Tor sites (with a .onion TLD) and building a front end to search them. It is currently called TorSearch (very creative, I know) and is available over Tor at [http://kbhpodhnfxl3clb4.onion][TS]. I have so far indexed over 110,000 pages in the current crawl; my previous crawl was decimated when FreedomHosting went down, apparently they had a very large portion of Tor sites.

[TS]: http://kbhpodhnfxl3clb4.onion.to

To handle the crawl, I started by trying to build my own crawler in Ruby and using [DelayedJob][dj] to handle the queuing of jobs and multiple workers to try to crawl through Tor's proxy interface. This turned out to be a horrible idea as it was unreasonably slow to attempt to crawl anything.

[dj]: https://github.com/collectiveidea/delayed_job_active_record

After looking around for other crawlers, I finally found [Apache Nutch][Nutch], an Apache project originally built by Yahoo to handle crawling the internet! It took some time to get Nutch setup correctly to use the Socks proxy that Tor exposes but after I got that working and tuned the threading to not overwhelm my Tor instance, I was off!

[Nutch]: https://nutch.apache.org/

I have currently run Nutch crawls using [HBase][HBase] as the data store for well over 7 days. After Nutch crawls the pages, I have it loading the pages into a [Solr][Solr] instance to make the pages available for searching. One of the really great things about Nutch is that it is tightly integrated with Solr so that they play well together.

[HBase]: https://hbase.apache.org
[Solr]: https://lucene.apache.org/solr/

One of the things that Nutch supports is to score documents based on the inbound link counts which it then also passes through to Solr allowing you to boost search results by the score field. What this means in practice is that you can score documents by inbound link text, title attribute, body text, and then boost that score by the boost field to get your final ordering, similar to how Google ranks pages.

## Watching the Searches

Since opening up TorSearch to the public (of Tor users) I have been shocked at times both by how normal some of the searches seem (WikiLeaks) and how odd others are (nercro dog porn). There have been over 13,000 unique queries since I opened up TorSearch with many queries being repeated several hundred times.

Initially, there was a very high volume of pornography related searches, both things that are fairly 'normal' and things that I would consider 'deviant' or otherwise creepy. Despite that though, I let the searches continue and was glad when the pornography related searches dwindled in relation to the overall search terms.