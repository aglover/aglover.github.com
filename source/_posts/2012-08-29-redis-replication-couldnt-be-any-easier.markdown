---
layout: post
title: "Redis replication: couldn't be any easier"
date: 2012-08-29 13:23
comments: true
categories: [Redis]
---

[Redis](http://redis.io) supports [master-slave replication](http://redis.io/topics/replication) and it's extremely easy to setup.  And the beauty of setting up replication is that all you have to do is fire up a slave instance and have it point to some other Redis instance (which then becomes the master node).

Accordingly, open up the `redis.conf` file for an intended secondary (or slave node) and find the section containing a `slaveof` phrase. Uncomment it and add the IP address & port of the master node:

```
slaveof ec2-23-21-a8-21.compute-1.amazonaws.com 6379
```

Then cycle the secondary node (so the `redis.conf` file is re-read). That's it. 

When you fire up the slave node and run the `INFO` command, you'll see a few new items, namely:

```
role:slave
master_host:ec2-23-21-a8-21.compute-1.amazonaws.com
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:1
master_sync_left_bytes:-1
master_sync_last_io_seconds_ago:0
master_link_down_since_seconds:14
```
You'll note that it takes a few moments for a data sync to occur, thus, after a few seconds, rerun the `INFO` command to see:

```
role:slave
master_host:ec2-23-21-a8-21.compute-1.amazonaws.com
master_port:6379
master_link_status:up
master_last_io_seconds_ago:6
master_sync_in_progress:0
```

Running the same `INFO` command on the master node will yield a new field as well:

```
role:master
slave0:10.255.3.143,34647,online
```

A master node can have 0..N slaves (hence the naming scheme of `slaveN`); what's more, slaves can become a master by commenting out or unsetting the `SLAVEOF` command in their corresponding configuration.

It should be noted that Redis does support configuration changes _at runtime_ via the `CONFIG SET` [command](http://redis.io/commands/config-set), which means you don't even need to cycle a particular node to synchronize it with a master. 
