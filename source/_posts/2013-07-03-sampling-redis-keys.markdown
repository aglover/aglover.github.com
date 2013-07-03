---
layout: post
title: "Sampling Redis keys for memory consumption"
date: 2013-07-03 14:05
comments: true
categories: Redis
---

{% img right /images/mine/redis_logo.png %}[We][5] run a farm of [Redis][0] instances for storing real time analytics. Because Redis stores objects in memory, it happens to be an extremely fast way to retrieve data; thus, most of our charts and graphs pull data from various Redis instances that contain desired data. 

Our Redis boxes are running in [AWS][3] on [m2.2xlarge instances][4], which contain a bit over 30GB of memory. Every once in awhile, it's helpful to get an idea of how much memory various key _patterns_ are consuming because we hold over 20GB across several hundred thousand keys in some cases. There's a nifty gem dubbed [redis-audit][1] ([my fork][2] of it adds [Bundler][6]) that is quite helpful in painting a broad picture of memory usage across a sampling of key patterns. 

<!-- more -->

Redis-audit works by sampling a configurable portion of keys residing in a Redis database. It then prints out a report of key patterns that delineates, among other things, memory usage. The summary portion of the output report looks something like:

``` bash redis-audit summary
------------------------------------+--------------+-------------------+---------------------------------------------------
Key                                 | Memory Usage | Expiry Proportion | Last Access Time
------------------------------------+--------------+-------------------+---------------------------------------------------
se::4f733211e::02/03/13::model::SGT | 19.03%    | 14.81%   | 1 days, 8 hours, 8 minutes, 40 seconds
se::4f7332a3e::03/08/13::manufactur | 8.76%     | 71.43%   | 6 hours, 14 minutes, 50 seconds
# more keys omitted
```

You can also get more details on individual key patterns; for example, the key above using 19% of Redis's memory is detailed like so:

``` bash redis-audit details for a key pattern
==============================================================================
Found 27 keys containing hashs, like:
se::4f7332a300011e::02/03/13::model::SGT SMA2+::aggregations, 
se::4f7332a300011e::06/06/13::model::SGH-M919V::aggregations, 
se::4f7332a300011e::05/19/13::model::vivo S7::aggregations,
se::4f7332a300011e::02/15/13::model::PAD707::aggregations, 
se::4f7332a300011e::06/12/13::model::Rise::aggregations, 
se::4f7332a300011e::04/12/13::model::Micromax A91::aggregations, 
se::4f7332a300011e::06/11/13::model::900TPCII::aggregations, 
se::4f7332a300011e::07/01/13::model::HUAWEI U8825D::aggregations, 
se::4f7332a300011e::02/20/13::model::AT7D-TE25DA::aggregations, 
se::4f7332a300011e::02/14/13::model::HUAWEI G510-0010::aggregations

These keys use 19.03% of the total sampled memory (990 bytes)
14.81% of these keys expire (4), with maximum ttl of 28 days, 15 hours, 51 minutes, 20 seconds
Average last accessed time: 69 days, 19 hours, 14 minutes, 15 seconds - (Max: 95 days, 23 hours, 10 minutes, 10 seconds Min:1 days, 8 hours, 8 minutes, 40 seconds)
```

This data is helpful in a number of ways; for instance, we discovered that a significant portion of memory was being consumed by keys containing old time series data that for some reason did not have associated TTLs. Thus, we were able to achieve that particular data (into [MongoDB][7]) and free up memory.

If you want to understand how memory is distributed across key patterns in a Redis instance, then I think you'll find [Redis-audit][1] quite helpful! 


[0]: http://redis.io/
[1]: https://github.com/snmaynard/redis-audit
[2]: https://github.com/aglover/redis-audit
[3]: http://thediscoblog.com/blog/categories/aws/
[4]: http://aws.amazon.com/ec2/instance-types/instance-details/
[5]: http://www.app47.com
[6]: http://bundler.io/
[7]: http://localhost:4000/blog/categories/mongodb/


