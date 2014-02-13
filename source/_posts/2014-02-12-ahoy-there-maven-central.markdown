---
layout: post
title: "Ahoy there Maven Central"
date: 2014-02-12 20:54
comments: true
categories: [Java, SQS, AWS]
---

[Ahoy!](https://github.com/aglover/ahoy), which is an asynchronous [SQS](http://thediscoblog.com/blog/categories/sqs/) adapter for [AWS's Java SQS library](http://thediscoblog.com/blog/categories/aws/), is now syncing with Maven Central. This means you can easily use Ahoy! in your Maven or Gradle builds.

For example, if you want to [spice up your SQS](http://thediscoblog.com/blog/2013/09/29/ahoy-there-callbacks/) and you use Maven, just add the following dependency for your `pom.xml` file and you'll be rockin' it in no time, baby! 

``` xml Including Ahoy! into your Maven pom.xml
<dependency>
	<groupId>com.github.aglover</groupId>
	<artifactId>ahoy</artifactId>
	<version>1.0.1</version>
</dependency>
```

You don't use Maven? But rather use Gradle? I've got you covered! 

``` groovy Adding Ahoy! into your Gradle build.gradle file
compile 'com.github.aglover:ahoy:1.0.1'
```

Check out [mvnrepository.com](http://mvnrepository.com/artifact/com.github.aglover/ahoy) for how to include Ahoy! into your SBT build or other dependency management tool like Ivy. 