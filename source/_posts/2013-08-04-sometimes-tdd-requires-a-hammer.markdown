---
layout: post
title: "Sometimes TDD requires a hammer"
date: 2013-08-04 11:08
comments: true
categories: [Java, TDD]
---

{% img left /images/mine/hammerj.jpg %}While there are myriad mocking libraries available for the Java platform, only a select few of these nifty frameworks is capable of mocking the non-mock-friendly modifiers of `static` and `final`. Static (or class) methods, while handy for factories, become a nuisance for a framework like [Mockito](https://code.google.com/p/mockito/), however, with the inclusion of [PowerMock](https://code.google.com/p/powermock/), you've got yourself a proverbial hammer.

[As I wrote about previously](http://thediscoblog.com/blog/2013/08/01/imbibing-the-fluency-of-mockito/), I had to deal with a 3rd party library that is used to integrate with a networked service. This library is essentially hardwired to interact with homebase, which naturally provides some challenges when trying to test ones code that relies on this library. Moreover, the said library contained a `static` method for creating instances of a specialized class, which, naturally, my code depended on. 

<!-- more -->

PowerMock is made to work in concert with either [EasyMock](http://easymock.org/) or [Mockito](https://code.google.com/p/powermock/wiki/MockitoUsage13); what's more, it comes with a custom runner for inclusion in [JUnit](https://github.com/junit-team/junit). I'm going to show you how to use PowerMock with Mockito as I happen to find Mockito's syntax much more fluent than EasyMock's. 

For starters, you'll need to use two class level annotations -- `@RunWith` for specifying the `PowerMockRunner` class (this is a JUnit annotation) and another dubbed `@PrepareForTest`, which takes the class with `static` methods you wish to mock. `@PrepareForTest` is provided by PowerMock. 

In my case, the class containing a `static` method is named `QTP`, accordingly; my test class looks like this:

``` java JUnit test class with class level annotations
@RunWith(PowerMockRunner.class)
@PrepareForTest(QTP.class)
public class CreateCommandTest {
  @Test
  public void testCreateRequest() throws Exception {}
}
```

Next, in your test method, you use `PowerMokito`'s `mockStatic` method, which takes the class (again) with static methods you wish to mock.

``` java JUnit test case using PowerMock & Mockito
@Test
public void testCreateRequest() throws Exception {
  PowerMockito.mockStatic(QTP.class);
  //....
}
```

You can then mock a static method on the class you've been passing around to `mockStatic` and the `@PrepareForTest` annotation like you would normally do with Mockito. For instance, I can use the `when` method to specify what I want to happen when this static method is invoked. 

``` java Using normal Mockito actions for mocking
@Test
public void testCreateRequest() throws Exception {
  PowerMockito.mockStatic(QTP.class);
  QTP qtpThing = mock(QTP.class); //normal Mockito mocking
  //QTP.create is static
  when(QTP.create("dm2q", "0C4F7501UDC8C1EB43B06C988")).thenReturn(qtpThing);
  //QTP.createRecord isn't static
  when(qtpThing.createRecord(any(Tckt.class))).thenReturn(new Long(1000000L));
  //...
}
```

Finally, you can use [PowerMock](http://metlos.wordpress.com/2012/09/14/the-dark-powers-of-powermock/) to ensure your mocked static method is actually invoked. The requirements here are a bit funky; that is, it requires you _first_ specify how many times with one line of code and then you _actually call the static method_. 

``` java Verifying your mocked static method was invoked
@Test
public void testCreateRequest() throws Exception {  
  //...
  PowerMockito.verifyStatic(Mockito.times(1));
  QTP.create("dm2q", "0C4F7501UDC8C1EB43B06C988");
  //...
}
```

Yeah, that's sorta confusing, I know. 

Nevertheless, as most people in the world of Java figured out long ago, [static methods](http://stackoverflow.com/questions/2671496/java-when-to-use-static-methods) are [somewhat difficult](http://misko.hevery.com/2008/12/15/static-methods-are-death-to-testability/) when it [comes to testing](http://blog.codecentric.de/en/2011/11/testing-and-mocking-of-static-methods-in-java/). That is, while a method that conceptually has no state, at first glance, seems straightforward enough to test, the [issues arise](http://stackoverflow.com/questions/2472690/in-java-is-there-any-disadvantage-to-static-methods-on-a-class) when that static method does something like hit a database or in my case, call a web service to a networked asset. There is no easy way to override such behavior (unless, of course, you pull out a hammer).

Static methods have a place. But when it comes to testing code, whether it be legacy or some 3rd party library, the `static` modifier requires a hammer and as I hope I've shown you, [PowerMock](https://code.google.com/p/powermock/) is that hammer.
