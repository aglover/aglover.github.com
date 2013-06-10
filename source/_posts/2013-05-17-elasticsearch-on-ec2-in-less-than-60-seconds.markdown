---
layout: post
title: "ElasticSearch on EC2 in less than 60 seconds"
date: 2013-05-17 17:24
comments: true
categories: [elasticsearch, AWS]
---

{% img left /images/mine/es-bonzai.jpg %}Curious to see what all the [ElasticSearch](http://www.elasticsearch.org/) hubbub is about? Wanna see it in action without a lot of elbow grease? Then look no further, friend -- in less than 60 seconds, I'll show you how to install [ElasticSearch](http://www.ibm.com/developerworks/java/library/j-javadev2-24/) on an [AWS AMI](http://aws.amazon.com/). 

You'll first [need an AWS account](http://www.drdobbs.com/web-development/getting-started-with-the-cloud-amazon-we/231601598) along with an SSH key pair. If you don't already have those two steps done, go ahead and do that. The steps that follow suggest a particular AMI; however, you are free to select the [instance type](http://aws.amazon.com/ec2/instance-types/). [Micro instance types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) are free to use; consequently, you can get [up and running with ElasticSearch](http://thediscoblog.com/blog/2013/05/14/the-democratization-of-search/) in less than a minute _for free_. 

<!-- more -->

Now that you've got an [AWS](http://www.ibm.com/developerworks/web/library/j-s3/) account and an SSH key pair, go ahead and create a new security group (or edit an existing one). It's important that the [following ports](http://www.elasticsearch.org/tutorials/elasticsearch-on-ec2/) are open: 

* 22 (required for SSH)
* 80 (ElasticSearch uses HTTP for standard API calls)
* 9200 (required for ElasticSearch)
* 9300 (required for ElasticSearch)

Next, fire up a Linux AMI. I, for example, prefer [ami-c30360aa](http://cloud-images.ubuntu.com/locator/ec2/ ) (this is Ubuntu Server version 13.04) and I configure the AMI to use the security group that I just covered. 

Now, SSH to your newly instantiated AMI.  Once on the AMI, you'll need to install Java. Never fear though, I've got you covered. All you need to do is run a handy script via the [Ubuntu-Equip project](https://github.com/aglover/ubuntu-equip), that I use frequently just for this sorta thing: 

``` bash installing Java
wget --no-check-certificate https://github.com/aglover/ubuntu-equip/raw/master/equip_java.sh && bash equip_java.sh
```

You'll need to accept the license from Oracle. Once that script completes, go ahead and type  `java -version` and you should see Oracle's JDK (i.e Java version "1.7.0_21"). 

Next, download and install ElasticSearch via another nifty [Ubuntu-Equip](https://github.com/aglover/ubuntu-equip) script: 

``` bash installing elasticsearch
wget --no-check-certificate https://github.com/aglover/ubuntu-equip/raw/master/equip_elasticsearch.sh && bash equip_elasticsearch.sh
```

This script doesn't start ElasticSearch for you; thus, go ahead and change directories into the `elasticsearch` directory and fire it up like so:

``` bash starting elasticsearch
~/elasticsearch$ bin/elasticsearch -f
```

Take a deep breath (but not too deep, as I need you to finish in less than 60 seconds) and find the Public DNS of the AMI you've been working on. Go ahead and copy it, then fire up a browser on your local machine and go to http://YOUR_AMI_DNS_NAME.com:9200/_plugin/inquisitor/ (be sure to note the port). 

By the way, [Inquisitor](https://github.com/polyfractal/elasticsearch-inquisitor) is a handy web application that lets you query your indexes. It was installed via the Ubuntu-Equip script -- this tool is invaluable in figuring out how to properly query your indexes. 

And that is it. In less than 60 seconds you've got ElasticSearch running in the cloud for you. Want to create a cluster? No problem, just follow these steps again to fire up another ElasticSearch instance and then [configure the cluster accordingly](http://www.elasticsearch.org/videos/three-nodes-and-one-cluster/). 

I've not gone over [configuring ElasticSearch](http://www.elasticsearch.org/guide/reference/setup/configuration/) nor have I showed you how to create ElasticSearch as a service on a Linux instance, but for one minute, what do you expect? 



