---
layout: post
title: "Ahoy there callbacks!"
date: 2013-09-29 16:30
comments: true
categories: [Java, JavaScript, AWS, SQS]
---

{% img right /images/mine/callback.jpg %}Because it's my bag, I like [JavaScript](http://thediscoblog.com/blog/categories/javascript/). In fact, I've grown to love JavaScritp's asynchronous [callback oriented style of programming](http://en.wikipedia.org/wiki/Callback_(computer_programming). Consequently, when I find myself in a non-JavaScript environment, say, like [Java](http://thediscoblog.com/blog/categories/java/), I tend to miss using callbacks. 

The good news is that you can _emulate_ asynchronous callbacks in Java. In fact, I did just that recently with a library I've dubbed [Ahoy!](https://github.com/aglover/ahoy), which is an asynchronous [SQS adapter](http://www.ibm.com/developerworks/library/j-javadev2-17/) for AWS's Java [SQS](http://aws.amazon.com/sqs/) library.

<!-- more -->

For the uninitiated, [SQS is a cloud based messaging platform](http://www.ibm.com/developerworks/library/j-javadev2-17/) -- with SQS you can create queues and put messages onto those queues, which can then be read -- later or immediately by some other process or the same exact process. All of this leverages Amazon’s massively redundant architecture to offer extremely high availability in the face of concurrent access. 

Asynchronous callbacks in Java can be achieved with two features: anonymous classes (containing one method) and Java's `java.util.concurrent` package. 

Because Java doesn't allow you to pass functions (or methods) _easily_ as a parameter, to simulate a callback, you can create an interface that contains one method, which basically mimics a function. In the case of Ahoy, there are two interfaces: `MessageSendCallback` and `MessageReceivedCallback` -- both have one method: `onSend` and `onReceive` respectively. Accordingly, Ahoy!''s primary class, dubbed `SQSAdapter` exposes two simple methods: `send` and `receive` and both take their related callback interface. 

The most straightforward callback to understand is the `receive` method. As you can imagine, `receive` is intended to handle behavior when a message is received off of a particular queue. Thus, the `receive` method is defined as follows:

``` java SQSAdapter's receive method
public void receive(final MessageReceivedCallback callback) {}
```

The `MessageReceivedCallback` interface looks like this:

``` java The MessageReceivedCallback interface
public interface MessageReceivedCallback {
    public void onReceive(String messageId, String message);
}
```

Note, the `onReceive` method takes a message id (which is particular to SQS) and the message itself -- which in the case of SQS is always a `String` (that `String` can hold anything you want, keep in mind: JSON, XML, byte sequence, etc). 

Thus, clients of Ahoy! provide the intended behavior for a message when it is received. This behavior could be to write something to a database, generate another message and send it on another queue, you name it.

Now the interesting part is the implementation of Ahoy!'s `receive` method. To achieve asynchronocity, I employed Java's `java.util.concurrent` package, which sadly, seems to be under appreciated. 

``` java The receive method's implementation with callback being invoked
private void receive(final AmazonSQS sqs, final String queueURL, final MessageReceivedCallback callback) {
  pool.execute(new Runnable() {
    public void run() {
      final List<Message> messages = sqs.receiveMessage(
              new ReceiveMessageRequest(queueURL).withMaxNumberOfMessages(10).withWaitTimeSeconds(20)).getMessages();
      if (messages.size() > 0) {
          for (final Message message : messages) {
            callback.onReceive(message.getMessageId(), message.getBody());
            sqs.deleteMessage(new DeleteMessageRequest(queueURL, message.getReceiptHandle()));
          }
      }
    }
  });
}
```

With a fixed Thread pool, a thread is created, which waits for messages to arrive on a particular queue; when one shows up, the passed in `MessageReceivedCalledback` is invoked for each message. 

For an example of how this works for clients of Ahoy!, here's a test case that verifies the execution of the callback:

``` java The receive method implemented
final boolean[] wasReceived = {false};
ahoy.receive(new MessageReceivedCallback() {
  public void onReceive(String messageId, String message) {
    wasReceived[0] = true;
    assertNotNull("message id was null", messageId);
    assertEquals("message wasn't " + origMessage, origMessage, message);
  }
});
```

Likewise, sending a message is similar -- a new `Runnable` instance is created, which sends a particular message and invokes the passed in `MessageSentCallback`'s `onSend` method, passing in the newly sent messages's id. 

``` java The send method is also asynchronous
private void send(final AmazonSQS sqs, final String queueURL, final String message, final MessageSentCallback callback) {
  pool.execute(new Runnable() {
	  public void run() {
      SendMessageResult res = sqs.sendMessage(new SendMessageRequest(queueURL, message));
      if (callback != null) {
        callback.onSend(res.getMessageId());
      }
	  }
  });
}
```

Incidentally, the AWS Java SDK _does provide an asynchronous client_; however, this client's implementation leverages Java's [Futures](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html). While [Futures are a neat concept](http://nurkiewicz.blogspot.com/2013/02/javautilconcurrentfuture-basics.html), Ahoy!'s implementation is more convenient (_at least for me and in the patterns of how I employ SQS_) than Futures as there isn't any polling involved once a message is sent or received. 

While callbacks aren't [necessarily supported](http://en.wikipedia.org/wiki/Function_object#In_Java) natively in Java, you can emulate them quite nicely and achieve the same level of code conciseness as what's common in JavaScript. And if you need a handy way to interface with AWS SQS, [then give Ahoy! a try](https://github.com/aglover/ahoy)! Can you dig it, man?

