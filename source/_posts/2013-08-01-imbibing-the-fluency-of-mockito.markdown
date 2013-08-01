---
layout: post
title: "Imbibing the fluency of Mockito"
date: 2013-08-01 15:37
comments: true
categories: Java
---


{% img right /images/mine/Mockito.jpg %}I recently found myself writing some code to integrate two disparate platforms. One of these systems is Java based and the other, while not written in Java, offers a Java API. I'll call these systems Foo and Bar, respectively. 

It became obvious before I had written a line of code, however, that testing the eventual adapter would require I explicitly mock the later system's API (i.e. Foo's) as all I had to go with was a jar file whose classes and methods made it clear they communicated with a live instance. 

<!-- more --> 

I spent a few cycles to see what's new in the world of Java mocking and I was pleased to see that my old friend [Mockito](https://code.google.com/p/mockito/) is still active and is indeed, still an [excellent tool](http://www.javaworld.com/community/node/3772) for general purpose mocking. For the uninitiated, [Mockito](http://refcardz.dzone.com/refcardz/mockito) is Java based mocking framework that:

{% blockquote Google code mockito project page https://code.google.com/p/mockito/  Why Drink it? %}
...tastes really good. It lets you write beautiful tests with [a] clean & simple API. Mockito doesn't give you hangover because the tests are very readable and they produce clean verification errors.
{% endblockquote %}

Indeed, Mockito offers a [simple, fluent API](https://code.google.com/p/easyb/wiki/UsingMockito) that allows you to precisely mock behaviors without a lot of fuss. For instance, the main facade to interface with Bar is via a `QTP` class that has methods like `logIn`, `logOut`, etc. Rather than relying on classes under test to actually invoke these methods, I can easily create mock instances of `QTP` with Mockito like so:

``` java Mocking an instance of QTP
QTP qtpThing = mock(QTP.class);
```

Where `mock` is a statically imported method from `org.mockito.Mockito`. With a mock instance, I can then dictate how I wish certain methods to behave, _provided I pass along this mocked instance to my classes under test_.

For example, the method `logIn` doesn't return anything; in fact, one must invoke that method first and then call another method to generate a ticket (or token), which will be used on subsequent method invocations. Thus, the adapter that I'm writing will receive some input values (from Foo in the form of XML) and the adapter will return a ticket (in the form of an XML document as per Foo's required XML schema). 

Accordingly, the test this interaction, I need to do two things:

* ensure that the `logIn` method was invoked with specific parameters 
* mock the response of a valid ticket, via the `getTicket` method

What's more, I'd also like to verify that a `logIn` failure results in a particular interaction from within my adapter code. Therefore, I'll need to mock out some exceptional behavior as well. 

In the case of mocking out a particular method, you simply chain together a few methods; in my case, `when` and `thenReturn` do the trick like so:

``` java Mocking the behavior of getTicket
when(qtpThing.getTicket()).thenReturn("test-ticket");
```

In the code above, when the `getTicket` method is invoked on my mock instance, the `String` "test-ticket" will be returned. 

Next, to ensure that `logIn` was invoked with parameters obtained from an incoming XML document, I can use Mockito's `verify` method.

``` java Using Mockito's verify to ensure proper interaction
verify(qtpThing, times(1)).logIn("some_value", "some_user_name", "password");
```

In this case, the `verify` method checks that `logIn` is invoked one time and that three particular `String` values are passed in. If these expectations are not met, Mockito will throw an exception (and your corresponding test case will fail).

Thus, my test case for verifying my adapter is quite simple, yet highly readable.

``` java JUnit test case for verifying logIn behavior
@Test
public void testLoginRequest() throws Exception {
  QTP qtpThing = mock(QTP.class);
  when(qtpThing.getTicket()).thenReturn("test-ticket");
  AdapterRequest request = new AdapterRequest(XML.read("etc/test-login-req.xml"));
  QbosAdapter adapter = new QbosAdapter();
  adapter.setQtpInstance(qtpThing);
  AdapterResponse adapterResponse = adapter.performAction(request);
  assertNotNull(adapterResponse);
  verify(qtpThing, times(1)).logIn("some_value", "some_user_name", "password");
  assertEquals("test-ticket", adapterResponse.getData().getText());
}
```

What if I need to simulate an exception thrown by the `QTP` object, ostensibly from an invalid parameter or incorrect credentials during a log in? Again, Mockito's fluent API makes this a breeze. 

In my case, I'd like the `logIn` method to throw one of the checked methods in its method signature named `UnknownQtpException`. You can do this via the `doThrow` and `when` methods. 

``` java Mocking out exceptions in Mockito
doThrow(new UnknownQtpException()).when(qtpThing).logIn("", "blah", "blah");
```

In the code above, I'm explicitly declaring that if the first parameter to the `logIn` command is blank, then my mocked `QTP` instance should throw `UnknownQtpException`. Putting everything together yields the following test case:

``` java Testing exceptional circumstances with JUnit & Mockito
@Test
public void testFailureLoginRequest() throws Exception {
  QTP qtpThing = mock(QTP.class);
  doThrow(new UnknownQtpException()).when(qtpThing).logIn("", "blah", "blah");
  XML xml = XML.read("etc/test-login-req-err.xml");
  AdapterRequest request = new AdapterRequest(xml);
  QbosAdapter adapter = new QbosAdapter();
  adapter.setQtpInstance(qtpThing);
  AdapterResponse adapterResponse = adapter.performAction(request);
  assertNotNull(adapterResponse);
  verify(qtpThing, times(1)).logIn("", "blah", "blah");
  assertEquals("FAILURE", adapterResponse.getData().getText());
}
```

The beauty, of course, is that my test cases effectively test my adapter code without relying on a third party system (in this case, Bar). This is naturally a time honored testing technique employable in any language with a mocking framework worth its salt!

If you find yourself writing some integration code in Java, then I can't recommend Mockito enough. [Mockito's API](http://www.javaworld.com/community/node/3772) is quite straightforward and it makes tests easy to comprehend. I mean, it makes tests easy to imbibe.  Dig it?
