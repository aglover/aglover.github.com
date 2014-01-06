---
layout: post
title: "HipChat's 1 billion messages served"
date: 2014-01-06 13:50
comments: true
categories: [Redis, elasticsearch]
---


There's an [instructive post on the HipChat blog](http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/) regarding how their product's message count has risen from 110 million to over a billion since they were acquired by Atlassian. Central to their scaling strategy to meet data growth was their migration from Lucene to [Elasticsearch](http://thediscoblog.com/blog/categories/elasticsearch/). 

<!-- more -->

In their words they:

{% blockquote Zuhaib Siddique http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/ How HipChat scales to 1 Billion Messages %}
kicked Lucene to the curb in favor of Elasticsearch.
{% endblockquote %}

Their reasoning? [Elasticsearch's cluttering](http://thediscoblog.com/blog/2013/09/03/effortless-elasticsearch-clustering/)  allows them to:

{% blockquote Zuhaib Siddique http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/ How HipChat scales to 1 Billion Messages %}
add more nodes to our cluster when we need more capacity, so we can handle extra load while concurrently serving requests. Moreover, the ability to have our shards replicated across the cluster means if we ever lose an instance, we can still continue serving requests, reducing the amount of time HipChat Search is offline.
{% endblockquote %}

The other two primary pieces of their architecture haven't changed: CouchDB and [Redis](http://thediscoblog.com/blog/categories/redis/). Interestingly, HipChat [shards](http://www.ibm.com/developerworks/library/j-javadev2-11/) Redis data:

{% blockquote Zuhaib Siddique http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/ How HipChat scales to 1 Billion Messages %}
we shard our data over 3 Redis servers, with each server having its own slave.
{% endblockquote %}

Sharding in [Redis](http://redis.io/) isn't supported out of the box and requires an application to determine how to partition data across shards.  I wonder how HipChat data is partitioned and if their strategy evenly smears data across their shards -- like with any sharing strategy, once you've sharded on a particular key, [it's extremely difficult to re-shard](http://www.javaworld.com/article/2073449/think-twice-before-sharding.html). 

"[How HipChat scales to 1 Billion Messages](http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/)" is a good read -- so what are you waiting for? Check [it out](http://blog.hipchat.com/2013/10/16/how-hipchat-scales-to-1-billion-messages/)! 
 