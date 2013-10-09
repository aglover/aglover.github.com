---
layout: post
title: "All other metrics are useless"
date: 2013-10-09 18:04
comments: true
categories: [AWS, SQS, Java]
---

{% img left /images/mine/time1.jpg %}When it comes to queues, whether they're implemented as [JMS](http://en.wikipedia.org/wiki/Java_Message_Service), database tables (i.e. what Ruby's [Delayed::Job](http://thediscoblog.com/blog/2013/06/10/backgrounding-tasks-in-heroku-with-delayed-job/) uses for a queue), or even [Amazon's SQS](http://aws.amazon.com/sqs/), the most common metric used to evaluate the state of a queue is its length. In essence, one derives an efficiency metric based upon how many messages are residing in a queue at any given time. If there are just a few messages, the queue is operating efficiently. If there are numerous messages, things are inefficient and alarms must be sounded. 

<!-- more -->

But what if you're in a consistently busy environment with extreme bursts where queues have the tendency to rapidly fill up? If you have sufficient workers _already running_ to handle that burst, do you need to fire up more? 

You can fire up more workers, but doing so might cost you. That is, you might have to provision new worker instances, such as [Heroku worker dynos](https://devcenter.heroku.com/articles/dynos) or AWS AMIs, which will end up costing you tangible money. And sometimes those worker instances take a few moments to fire up and when they're operational, the burst of activity is over and the queue is back to normal -- the initially available workers handled the load adequately. 

It turns out that the queue's length was a lagging indicator. You spun up unneeded resources. False alarm!

If you already have sufficient capacity to handle the influx of messages on a queue, then monitoring a queue's length isn't too helpful. In fact, it's a misleading metric and can cause you to take unneeded actions. 

Consequently, a queue's length _is not indicative of a system's efficiency_ when there's already sufficient workers present. Rather, the metric that means something in a high capacity environment is _how long a message resides in a queue_. That is an actionable metric: if messages are stuck in a queue waiting to be processed then you need more processors!

## Moo over queue length and let queue wait time in

By default, [Amazon's SQS](http://www.ibm.com/developerworks/library/j-javadev2-17/) doesn't provide the ability to query how long a message has been residing in a queue. Therefore, [I wrote Moo](https://github.com/aglover/moo). 

Moo provides an interface for clients to obtain and take action on the message time in queue metric. This is done by augmenting an SQS message with a time stamp. That time stamp is then checked when a message is popped off of an SQS queue. If a threshold difference is exceed, then a callback is invoked. 

Users of Moo will find its usage similar to [Ahoy!](https://github.com/aglover/ahoy), which is an asynchronous callback oriented facade on top of [AWS's Java SDK](http://aws.amazon.com/sdkforjava/). In fact, Moo uses Ahoy! underneath, with the added feature of attaching a "maximum time in queue" asynchronous callback.

Moo supports multiple time in queue thresholds and setting a maximum time in queue threshold is done like so:

``` java Adding a maximum threshold for time in queue
//adds a 1 second max threshold
sqs.addQueueWaitTimeCallback(1000, new QueueWaitTimeCallback() {
  public void onThresholdExceeded(long waitTime) {
    //waitTime is the actual time in queue
    //do something... like fire off a web hook, etc
  }
});
```

Note the `addQueueWaitTimeCallback` method takes a millisecond maximum time in queue value and an accompanying `QueueWaitTimeCallback` callback implementation. The `onThresholdExceeded` method will be invoked asynchronously during a message receive if the maximum threshold value is exceeded; what's more, the `onThresholdExceeded` will receive as a parameter the actual queue wait time.

#### Show me the Moo

To fire up an instance of Moo, you have a number of options, including configuring an instance of AWS's `AmazonSQS` or just passing along a key, secret, and queue name like so:

```
SQS sqs = new SQS(System.getProperty("key"), System.getProperty("secret"), System.getProperty("queue"));
``` 

Next, you can attach zero to many `QueueWaitTimeCallback` instances like so:

```
sqs.addQueueWaitTimeCallback(600000, new QueueWaitTimeCallback() {
  public void onThresholdExceeded(long actualWaitTime) {
    //do something -- fire off SNS message?
  }
});
```

In this case, I've added a callback to be invoked if messages are in a queue longer than 10 minutes. Note, these `QueueWaitTimeCallback` callbacks are fired by the *queue reader* instance; accordingly, a `QueueWaitTimeCallback` can certainly fire up more instances of itself, for example.

Here's a sample JSON document that you might want to throw onto an SQS queue: 

```
{ "employees":[
      { "firstName":"John", "lastName":"Doe" },
      { "firstName":"Anna", "lastName":"Smith" },
      { "firstName":"Peter", "lastName":"Jones" }
]}
```

Sending and receiving this message are exactly like you'd do if you were using Ahoy!. For example, to send a message, just pass along a `String` to the `send` method:

```
sqs.send(json, new SendCallback() {
  public void onSend(String messageId) {
    //messageId is from SQS
  }
});
```

Note, the `send` method takes an optional `SendCallback`. 

Receiving a message is via the `receive` method, which takes a mandatory `ReceiveCallback` -- this callback will be invoked asynchronously _for each_ message received off of a queue. Each instance will receive the message placed upon the queue and the message's SQS id. 

```
sqs.receive(new ReceiveCallback() {
  public void onReceive(String messageId, String message) {
    //do something w/the message -- in this case it's JSON
  }
});
```

Note, if upon the receive of a message, Moo notices that a message has been waiting in a queue for more than the max queue wait time threshold configured for an associated `QueueWaitTimeCallback`, Moo will invoke it. Note, Moo can invoke more than one instance; thus, you can set up a chain to take various actions as times increase.


Remember, a queue's length is usually a lagging indicator.  The metric that actually means something is how long a message resides in a queue. That's an actionable metric and [Moo](https://github.com/aglover/moo) gives you the ability to do something about it! Can you dig it?


