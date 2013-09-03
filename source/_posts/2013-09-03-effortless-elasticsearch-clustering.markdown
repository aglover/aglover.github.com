---
layout: post
title: "Effortless ElasticSearch clustering"
date: 2013-09-03 07:50
comments: true
categories: elasticsearch
---

{% img left /images/mine/cluster.jpg %}[ElasticSearch](http://thediscoblog.com/blog/categories/elasticsearch/) supports [clustering](http://www.elasticsearch.org/guide/reference/modules/cluster/); that is, you can have a series of distinct [ElasticSearch](http://thediscoblog.com/blog/2013/05/14/the-democratization-of-search/) instances work in a coordinated manner without much administrative intervention at all. Clustering ElasticSearch instances (or nodes) provides data redundancy as well as data availability. 

Best of all, clustering in ElasticSearch, by default, doesn't require _any_ configuration -- nodes discover each other. You can set up a cluster in about 60 seconds. Let me show you how!

<!-- more --> 

First, download and unzip or untar the [latest version of ElasticSearch](http://thediscoblog.com/blog/2013/01/02/scalable-searching-with-elasticsearch/). Next, copy the resultant ElasticSearch install directory (for example, mine is dubbed `elasticsearch-0.90.3`) into 3 different directories; for example, I've called my directories `node-1`, `node-2`, and `node-3`. 

Next, open up three terminal windows and in each, change directories to a sequential node. Start the instance in the `node-1` directory like so:

``` bash Starting up ElasticSearch
./bin/elasticsearch -f 
```

The `-f` forces the process to run in the foreground. 

By default, ElasticSearch nodes will name themselves if you don't provide a name (via the `elasticsearch.yml` configuration file). Thus, after you start the first instance, you should see something along the lines of:

``` bash A master node is created!
[cluster.service] [Dionysus] new_master [Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]], reason: zen-disco-join (elected_as_master)
```

In the above log output, "Dionysus" is the automatic name chosen by ElasticSearch. Note the part about "new_master" for the `cluster.service`. 

Next, go into the next terminal window, say `node-2`, and start that instance the same way (via the `-f` flag). You should see the 2nd instance (in my case, named `Caiera`) discover the master:

``` bash Node #2 discovers the master node
[cluster.service] [Caiera] detected_master [Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]], added {[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]],}, reason: zen-disco-receive(from master [[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]]])
```

See the `detected_master` statement above?  And you should see the master, "Dionysus", add the 2nd instance, "Caiera", too (via the first terminal window):

``` bash The master node adds node #2
[cluster.service] [Dionysus] added {[Caiera][Eh3DHlcRQhGxatGnUG8smA][inet[/192.168.1.12:9301]],}, reason: zen-disco-receive(join from node[[Caiera][Eh3DHlcRQhGxatGnUG8smA][inet[/192.168.1.12:9301]]]) 
```

Lastly, start the 3rd instance in the 3rd window. 

In this case, my 3rd node is dubbed "Phantom Rider" and it'll discover the master:

``` bash Node #3 discovers the master
[cluster.service] [Phantom Rider] detected_master [Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]], added {[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]],[Caiera][Eh3DHlcRQhGxatGnUG8smA][inet[/192.168.1.12:9301]],}, reason: zen-disco-receive(from master [[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]]]) 
```

And the master, will in turn, add "Phantom Rider" into the cluster:

``` bash The master node adds node #3
[cluster.service] [Dionysus] added {[Phantom Rider][Sw1bDSbFQqeTq4M45qbNpg][inet[/192.168.1.12:9302]],}, reason: zen-disco-receive(join from node[[Phantom Rider][Sw1bDSbFQqeTq4M45qbNpg][inet[/192.168.1.12:9302]]])
```

Now that you have 3 ElasticSearch instances running, you can run a few RESTful commands to verify your cluster is operational. 

First, in a new terminal window, run this command:

``` bash cURL to ascertain cluster nodes
curl -XGET 'http://localhost:9200/_cluster/nodes?pretty=true'
```

You should see a nicely formatting JSON response. It'll look something like:

``` json ElasticSearch nodes
{ 
  "cluster_name" : "elasticsearch",
  "nodes" : { "Eh3DHlcRQhGxatGnUG8smA" : { "hostname" : "new-host-5.home",
          "http_address" : "inet[/192.168.1.12:9201]",
          "name" : "Caiera",
          "transport_address" : "inet[/192.168.1.12:9301]",
          "version" : "0.90.3"
        },
      "Sw1bDSbFQqeTq4M45qbNpg" : { "hostname" : "new-host-5.home",
          "http_address" : "inet[/192.168.1.12:9202]",
          "name" : "Phantom Rider",
          "transport_address" : "inet[/192.168.1.12:9302]",
          "version" : "0.90.3"
        },
      "r7gbosdKSWGfTCgRPrS6vw" : { "hostname" : "new-host-5.home",
          "http_address" : "inet[/192.168.1.12:9200]",
          "name" : "Dionysus",
          "transport_address" : "inet[/192.168.1.12:9300]",
          "version" : "0.90.3"
        }
    },
  "ok" : true
}
```

Note the `cluster_name` is by default elasticsearch -- you can change this name as well. Also, by default, the master node will claim the 9200 port, however, you can run that same command against any node (for example, `http://localhost:9201/_cluster/nodes?pretty=true` will respond with the same exact response). 

You can check the health of a cluster, as well, by running this command:

``` bash cURL to ascertain cluster health
curl -XGET 'http://localhost:9200/_cluster/health?pretty=true' 
```

And you should see another JSON response like:

``` json ElasticSearch cluster health
{ 
  "active_primary_shards" : 0,
  "active_shards" : 0,
  "cluster_name" : "elasticsearch",
  "initializing_shards" : 0,
  "number_of_data_nodes" : 3,
  "number_of_nodes" : 3,
  "relocating_shards" : 0,
  "status" : "green",
  "timed_out" : false,
  "unassigned_shards" : 0
}
```

Nodes a cluster will elect a new master if your master node goes down. For example, if you go into the master node terminal window and control-c the process, you should see the two other nodes quickly recognize the master's failure and consequently elect one of themselves as the new master:

``` bash A new master is elected
[discovery.zen] [Caiera] master_left [[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]]], reason [shut_down]
[cluster.service] [Caiera] master {new [Caiera][Eh3DHlcRQhGxatGnUG8smA][inet[/192.168.1.12:9301]], previous [Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]]}, removed {[Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]],}, reason: zen-disco-master_failed ([Dionysus][r7gbosdKSWGfTCgRPrS6vw][inet[/192.168.1.12:9300]])
```

Remember, ElasticSearch will make intelligent defaults for you, however, you should most likely look closely at the various aspects you can configure when it comes to clustering. For example, cluster name and node names are something you should consider implementing. 

What's more, the process of discovery can be configured. For instance, multicast is used for auto-discovery, however, there are other options available, such as unicast, which allows you to specify which nodes can be a part of a cluster (that is, unknown nodes cannot join). 

Finally, you can control how nodes operate in a cluster. You can explicitly forbid a node from being a master, for example, and you can also configure nodes to not hold data (thus, those nodes become veritable routers for searches). These options allow you to create an interesting topology that has some intelligent routing built in. 

Regardless of how you configure an ElasticSearch cluster, I hope you've seen that it couldn't be any easier. Can you dig it, man?