---
layout: post
title: "Elasticsearch in a box"
date: 2013-11-25 13:42
comments: true
categories: [Java, elasticsearch, Linux, DevOps, Ubuntu]
---

{% img left /images/mine/esbox.jpg %}Are you looking to get going with [Elasticsearch](http://thediscoblog.com/blog/categories/elasticsearch/) as quickly as possible without having to worry about [installing Java or Elasticsearch](http://thediscoblog.com/blog/2013/05/17/elasticsearch-on-ec2-in-less-than-60-seconds/) itself? Are you looking for a repeatable and automated mechanism for bringing up Elasticsearch instances for developmental and or testing purposes? While there's certainly a number of [Elasticsearch](http://www.elasticsearch.org/)-as-a-platform service providers out there, there's one other option: use [Elasticsearch-in-a-box](https://github.com/aglover/coffer).

Elasticsearch-in-a-box is a freely available [Vagrant base box](http://www.vagrantbox.es/). What that means is that you can quickly fire up and tear down an Elasticsearch environment with [simple commands](http://docs.vagrantup.com/v2/getting-started/) like `vagrant up` and `vagrant destroy`. 

<!-- more -->

In order to use Elasticsearch-in-a-box, you first need to have [Vagrant](http://docs.vagrantup.com/v2/installation/) and [VirtualBox](https://www.virtualbox.org/) installed. These two installations couldn't be any easier. To install [Vagrant](http://thediscoblog.com/blog/2013/10/16/ssh-and-vagrant/), simply go to the downloads page and pick your target distribution. Vagrant provisions machines on top of virtual machine providers like VMWare, [AWS](http://thediscoblog.com/blog/categories/aws/), and VirtualBox. VirtualBox is free and easy to install -- like Vagrant, simply [go to the downloads section](https://www.virtualbox.org/wiki/Downloads) and pick your target platform. 

Once you have both Vagrant and VirtualBox installed, you are two steps away from Elasticsearch-ing. 

First, you need to add and initialize the Elasticsearch-in-a-box [template](http://docs.vagrantup.com/v2/boxes.html). Go ahead and create a directory, like `/projects/esinabox`, change directories into it and execute this command:

``` bash This command will create a Vagrant definition named esinabox from the downloaded template
vagrant box add esinabox https://s3.amazonaws.com/coffers/esinabox.box
```

This command will download the Elasticsearch-in-a-box template. Once that completes (it'll take a few moments depending on your connection), execute this command:

``` bash Vagrant init will create a VagrantFile
vagrant init 'esinabox'
```


This command will create a `VagrantFile`, which you can use to customize the Elasticsearch-in-a-box instance. By default, you shouldn't need to do much, however, you can map network ports, install additional software via [Bash](http://thediscoblog.com/blog/2013/11/18/provisioning-ubuntu-with-java-in-3-steps/), Chef, and Puppet at your discretion. 

Next, fire up Elasticsearch-in-a-box like so:

``` bash Starting up Elasticsearch-in-a-box
vagrant up
```

Now that Elasticsearch-in-a-box is running locally on your machine, you can open up a new terminal and execute RESTful commands like normal because Elasticsearch is running on same ports: 9200 & 9300. So go ahead and execute some queries, like so:

``` bash Elasticsearch is up an running!
curl -XGET 'http://localhost:9200/_status?pretty=true'
```

And when you are done, go ahead and tear down the instance like so:

``` bash Destroying a VM instance
vagrant destroy -f 
```

Wasn't that easy? The Elasticsearch-in-a-box Vagrant template was built using [Veewee](https://github.com/jedi4ever/veewee). The base box is 64-bit [Ubuntu 12.04](http://www.ubuntu.com/index_asus.html) with Oracle's [Java 7](http://thediscoblog.com/blog/2013/11/18/provisioning-ubuntu-with-java-in-3-steps/) and [Elasticsearch version 0.90.7](http://www.elasticsearch.org/download/). 

If you're looking for a quick and easy way to automatically provision Elasticsearch, then look no further and give [Elasticsearch-in-a-box](https://github.com/aglover/coffer) a try! 