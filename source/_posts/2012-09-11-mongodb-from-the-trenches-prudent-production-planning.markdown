---
layout: post
title: "MongoDB from the trenches: prudent production planning"
date: 2012-09-11 11:34
comments: true
categories: [MongoDB]
---

{% img left /images/mine/Mongodb_icon.png %}While starting out with [MongoDB](http://http://www.Mongodb.org/) is super easy, there are few things you should keep in mind as you move from a development environment into a production one. No one wants to get paged at 3am because a customer can’t complete an order on your awesome e-commerce site because your database isn’t responding fast enough or worse, is down.


Planning for a production deployment with MongoDB [isn't rocket science](http://www.mongodb.org/display/DOCS/Production+Notes), but I must warn you, it'll cost money, especially if your application actually gets used _a lot_, which is every developer's dream. Therefore, like all databases, you need to plan for high availability and you’ll want the maximum performance benefits you can get for your money in a production environment. 

First and foremost, [Mongo likes memory](http://www.mongodb.org/display/DOCS/Caching); that is, frequently accessed data is stored directly in memory; moreover, writes are also stored in memory until being flushed to disk. It’s imperative that you provide enough memory for Mongo to store a valid working dataset; otherwise, Mongo will have to go to the disk to retrieve, what should be, fast lookups via indexed data. This is sloooooow. Therefore, a good rule of thumb is to plan to run your Mongo instances with _as much memory as you can afford_. 

You can get an idea for your working data set by running `Mongostat` -- this is a handy command line utility that’ll give you a second-by-second view into what Mongo is up to -- one particular metric you’ll see is resident memory (labeled as `res`) -- this will give you a good idea of how much memory Mongo’s using at any given moment. If this number exceeds what you have available on a given machine, then Mongo is having to go to disk, which is going to be a lot slower. 

Not all data can be stored in memory; every document in Mongo is eventually written to disk. And like always, I/O is always a slow operation compared to working with memory. This is why, for example, writes in Mongo can be so fast -- drivers allow you to, essentially, fire and forget and the actual write to disk is done later, asynchronously. Reads can also incur an I/O penalty when something requested isn’t in working memory. 

Thus, for high performance reads and writes, _pay attention to the underlying disks_. A key metric here is IOPS or input/output operations per second. Mongo will be extremely happy, for example, [in an SSD environment](http://www.mongodb.org/display/DOCS/SSD), provided you can afford it. [Just take a look at various IOPS comparisons between SSDs and traditional spinning disks](http://en.wikipedia.org/wiki/IOPS) -- super fast RPM disks can achieve IOPS in the 200 range. Typical SSD drives are attaining wild numbers, orders of magnitude higher (like in the 100’s of thousands of IOPS). It’s crazy how fast SSDs are compared to traditional hard drives. 

RAM is still faster than SSDs, so you’ll still want to understand your working set of data and ensure you have plenty of memory to contain it.  

Finally, for maximum availably, _you really should be using Mongo’s [replica sets](http://www.mongodb.org/display/DOCS/Replica+Sets)_. Setting up a cluster of Mongo instances is so incredibility easy that there really isn’t a good reason not to do it. The benefits of doing so are manifold, including: 

 * data redundancy
 * high availability via automated failover
 * disaster recovery. 

Plus, running a replica set makes maintenance so much easier as you can bring nodes off line and on line w/out an interruption of service. And you can run nodes in a replica set on commodity hardware (don’t forget about my points regarding memory and I/O though). 

Accordingly, when looking to move Mongo into a production environment, you need to consider memory, I/O performance, and replica sets. Running a high performant, high availability replica set'ed Mongo, not surprisingly, will cost you. If you're looking for options for running Mongo in a production environment, I can't recommend enough the team at [MongoHQ](https://mongohq.com/). 

I'm a huge fan of Mongo. Check out some of the articles, videos, and podcasts that I've done, which focus on Mongo, including:

 * [Java development 2.0: MongoDB: A NoSQL datastore with (all the right) RDBMS moves](http://www.ibm.com/developerworks/java/library/j-javadev2-12/)
 * [Video demo: An introduction to MongoDB](http://public.dhe.ibm.com/software/dw/demos/jmongodb/index.html)
 * [Eliot Horowitz on MongoDB](http://www.ibm.com/developerworks/java/library/j-gloverpodcast/#Horowitz)
 * [10gen's Steve Francia talks MongoDB](http://www.ibm.com/developerworks/java/library/j-gloverpodcast4/index.html#francia)
