---
layout: post
title: "The democratization of search"
date: 2013-05-14 11:33
comments: true
categories: elasticsearch
---

{% img right /images/mine/democracy.jpg %}Over the past year and a half, I've watched [ElasticSearch][1] grow from a seemingly part-time code experiment into a thriving ecosystem. Not only has the number of [client libraries][2] grown from 1 to over 25 (and counting!); it's now a [commercially sponsored project][3] to the tune of [$34 million][9] (a [$10M series A][8] and a [$24M series B][10]) with 200,000 downloads a month. 

Search is the touchstone of the Internet; without search the Internet wouldn't be all that useful. Google's meteoric rise and its resultant eponymous name for search is clear evidence as to the importance of search. Search, however, hasn't always been that easy nor affordable to implement. 

<!--more-->

Before the likes of open source projects like [Lucene][4], implementing search in an application involved an expensive commercial product or a series of SQL `like` statements that were never really good enough. Lucene, however, isn't a simple hobbyist's pursuit. Lucene requires a lot of expertise; what's more, successful projects within the Lucene ecosystem, like [Solr][5] lack a key feature that is defining modern architectures: [distributed][6]. 

The continued low cost of storage combined with cheap rent-able infrastructures like [AWS](http://www.drdobbs.com/web-development/getting-started-with-the-cloud-amazon-we/231601598) has made it convenient to store a plethora of data, thus bringing to bear the importance that applications must support searching vast amounts of data, do it quickly, and affordably. This is where [ElasticSearch shines][7]. 

[ElasticSearch sits on top of Lucene](http://www.elasticsearch.org/overview/) and adds not only a simple API for adding and searching content, but does it in a distributed manner. With infinitesimal arm grease, you can set up a search cluster that smears your data and resultant queries across a series of nodes. Not only is this resultant architecture fast, but it's easy to set up and extremely affordable as search nodes can run on commodity hardware. In essence, ElasticSearch brings search to the masses. 

Google and its resultant ease of search has changed the mindset of application users. Searching content is a _presumed feature_ and if you don't provide it, you're already a few steps behind your competition. ElasticSearch is clearly a path to constructing a viable, easy to install, affordable, distributed search infrastructure for any application. If you don't believe me, take a look at some of the innovative companies with substantial amounts of users using it: [Github](https://github.com/), [foursquare](https://foursquare.com/), and [Stack Overflow](http://stackoverflow.com/) are just a few. 

[1]: http://www.elasticsearch.org/
[2]: http://www.elasticsearch.org/guide/clients/
[3]: http://www.elasticsearch.com/
[4]: http://lucene.apache.org/
[5]: http://lucene.apache.org/solr/
[6]: http://www.searchblox.com/solr-vs-elasticsearch
[7]: http://www.ibm.com/developerworks/java/library/j-javadev2-24/
[8]: http://www.zdnet.com/elasticsearch-raises-24-million-big-data-analytics-7000011496/
[9]: http://www.whiteboardmag.com/youre-hot-or-not-why-elasticsearch-raised-24-million-just-3-months-after-a-10-million-round/
[10]: http://gigaom.com/2013/02/19/open-source-search-tool-elasticsearch-gets-24m/

