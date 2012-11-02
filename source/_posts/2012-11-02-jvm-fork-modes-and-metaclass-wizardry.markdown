---
layout: post
title: "JVM fork modes and metaclass wizardry"
date: 2012-11-02 16:30
comments: true
categories: [Java]
---

Over the past decade of finding myself staring at innumerable Ant build files containing a [JUnit task](http://ant.apache.org/manual/Tasks/junit.html), I've come to realize a subtle, yet powerful flag, that when not set properly, almost always leads to random and confusing test catastrophes. The JUnit task supports a number of attributes, but the most important is the `fork` one. This flag controls whether or not the tests being run are invoked within the same JVM instance of Ant itself or within a new one. By default, this value is set to `off` (`false` seems to do the same thing); however, in doing so, I've, from time to time, seen class loading issues or mysterious versions of jars causing strange code to be executed. 

Thus, unless there is some reason _not_ to do so, I usually set the the `fork` attribute to `true`. But alas, the story doesn't end there! With the value of `true`, a test suite will be executed within a new JVM, shielded from Ant and 98.72% of the time, this is good enough. Nevertheless, if your code does something _really_ interesting, like augment normal Java code with [MetaClass wizardry](http://groovy.codehaus.org/Per-Instance+MetaClass) you might find yourself scratching your head, especially if you change the behavior of the same classes between tests. 

For instance, at [App47](http://www.app47.com/) we have a number of different software components, written in various languages, that interact via queues. In one particular case, we have a [Rails](https://cirrus.app47.com) instance dropping messages on two queues that are ultimately processed by a Java application. The underlying queue implementation changes based upon where the code is running (i.e. in the cloud or within a data center). The data center queuing implementation was added after an initial cloud implementation was written an deployed. In order for the Java application to interact with the underlying queues and their differing implementations, at runtime, one class is dynamically augmented with some extra code, unique to the particular queue it is set to interact with. This magic is actually quite simple and is achieved via Groovy. 

Essentially, a `Message` class (representing a generic message residing on a queue) is dynamically augmented with a `Processor` class that knows how to actually handle the message instance. In this case, we only have two types of messages, so at run time, when the actual Java process fires up, it is told which type of `Processor` to shove into a popped `Message` instance like so:

``` java Groovy MetaClass wizardry
def iprocessor = manufactureProcessor(processor)
iprocessor.setProperties(properties)
Message.metaClass.static.getMessageProcessor << { return iprocessor }
```

Thus, when a particular `Message` instance is popped off of a queue, it asks itself for an instance of a `Processor` via the `getMessageProcessor` method and it proceeded to process itself. Without delving into the particulars of underlying queues or workflow, the relevant details here are that the original message was created via a Ruby process and that ultimately the underlying message is a JSON document; what's more, the dynamic code was added to retro fit a newer queueing technology required for data center deployments. 

Now back to JUnit and Ant: this varying and dynamic altering of a Java class caused some subtle test failures when run in a forked mode.  It turns out that in addition to the ability to fork the JVM for a test suite, you can control the granularity of how often you can fork. That is, via the `forkmode` attribute, you can fork one time for all tests or once for _each test_.     

The `forkmode` attribute can take the values of `perTest` (which is the default value), `once`, and `perBatch` -- in our particular case, the value was initially set to `once`, which meant that once a `Message` class was dynamically set with a `Processor`, it seemingly always had the _same one_. That is, even in later tests where the underlying logic was setting different `Processor` instances, they were subsequently ignored in favor of which ever `Processor` was initially set.  Switching the `forkmode` attribute to `perTest` naturally fixed the test failures. This made total sense: each JVM instance for each test had the correctly modified `Message` class. This incidentally also models how the underlying Java processes work in a production environment: they each have their own JVM. 

Therefore, pay particular attention to the settings in an Ant file's JUnit task. The same is true for Maven's [Surefire Plugin](http://maven.apache.org/plugins/maven-surefire-plugin/) -- in both cases, the fork mode and the frequency at which tests fork can mean the difference between a successful test suite run or a cacophony of failures. 
