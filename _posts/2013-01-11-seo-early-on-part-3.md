---
layout: single
title: "SEO Early On, Part 3"
description: "Continuing the series on SEO for young businesses"
category: SEO
tags: [SEO,Startup]
blog: true
comments: true
redirect_from: /blog/2013/01/11/seo-early-on-part-3/
---

On a slightly more technical note than my previous 2 posts ([Part 1][] and [Part 2][]) on SEO, page speed is quite important.  There are some great tools for checking on your page speed both locally and from a remote location and debugging any speed issues that you see.

[Part 1]: {% post_url 2012-12-17-seo-early-on %})
[Part 2]: {% post_url 2012-12-28-seo-early-on-part-2 %}

- [Google Chrome][] and [Safari][]
  : - Developer Tools (Accessible via f-12 or the tools menu), Network Tab
- [Firefox][]
  : - [Firebug][] is a great add-on for checking your site
- Anywhere
  : - [Pingdom][] has a great tool for checking page load from various places

[Google Chrome]: http://chrome.google.com
[Safari]: http://www.apple.com/safari/
[Firefox]: http://getfirefox.com
[Firebug]: http://getfirebug.com
[Pingdom]: http://tools.pingdom.com/fpt/

Some common problems with page speed are caching resources correctly and high latency.  Both problems can be solved with an easy to implement CDN such as [Cloudflare][] or a slightly more complex (but potentially higher powerful) [Amazon Cloudfront][].

[Cloudflare]: https://clourflare.com
[Amazon Cloudfront]: http://aws.amazon.com/cloudfront/

I am using Cloudflare on this site and it was both easy to setup and free but also gives me some nice analytics that Google Analytics doesn't include because Cloudflare can track requests that don't load Javascript.

While I am suggesting that you check out your sites response times and that you work on reducing those times, page speed can be a difficult task to handle and should definitely NOT be at the top of your priority list when you're looking for something to improve.