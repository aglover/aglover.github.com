---
layout: post
title: "Java 8's functional fomentation"
date: 2014-03-25 20:39
comments: true
categories: Java
---

{% img right /images/mine/revolution_small.png %}Java 8 has revolutionized Java. It's easily the most significant release of Java in the last [10 years](http://en.wikipedia.org/wiki/Java_version_history#J2SE_5.0_.28September_30.2C_2004.29). There are a ton of new features including default methods, method and constructor references, and lambdas, [just to name a few](http://winterbe.com/posts/2014/03/16/java-8-tutorial/). 

One of the more interesting features is the new [`java.util.stream`](http://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) API, which as the Javadoc states, enables

{% blockquote  http://docs.oracle.com/javase/8/docs/api/ Package java.util.stream Description %}
functional-style operations on streams of elements, such as map-reduce transformations on collections
{% endblockquote %}

Combine this new API with lambda expressions and you end up with a terse, yet, powerful syntax that significantly simplifies code through the application of projections. 

<!-- more --> 

Take, for example, the ostensibly simple task of filtering a collection. In this case, a simple `Collection` of `Message` types, created like so:

``` java Creating a Collection of Messages
List<Message> messages = new ArrayList<>();
messages.add(new Message("aglover", "foo", 56854));
messages.add(new Message("aglover", "foo", 85));
messages.add(new Message("aglover", "bar", 9999));
messages.add(new Message("rsmith", "foo", 4564));
```

With this collection, I'd like to filter out `Message`s with a `delay` (3rd constructor parameter) greater than 3,000 seconds. 

[Previous to Java 8](http://stackoverflow.com/questions/122105/java-what-is-the-best-way-to-filter-a-collection), you could hand jam this sort of logic like so:

``` java Filtering old school style
for (Message message : messages) {
  if (message.delay > 3000) {
    System.out.println(message);
  }
}
```

In Java 8, however, this job becomes a lot more concise. Collections now support the `stream` method, which converts the underlying data structure into a iterate-able steam of objects and thereby permits a new breed of functional operations that leverage lambda expressions. Most of these operations can be chained as well. These chain-able methods are dubbed _intermediate_, methods that cannot be chained are denoted as _terminal_. 

Briefly, lambda expressions are a lot like anonymous classes except with _a lot less_ syntax. For example, if you look at the Javadocs for the parameter to a `Stream`'s `filter` method, you'll see that it takes a [`Predicate`](http://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) type. Yet, you don't have to implement this interface as you would, say, before Java 8 with an anonymous class. Consequently, the `Predicate` lambda expression for filtering all values of `delay` greater than 3000 would be:

``` java Lambda expression
x -> x.delay > 3000
```

Where `x` is the parameter passed in for _each value_ in the stream and everything to the right of the `->` being the expression evaluated. 

Putting this all together in Java 8 yields:

``` java Streaming lambdas!
messages.stream().filter(m -> m.delay > 3000).forEach(item -> System.out.println(item));
```

Interestingly, due to some other new features of Java 8, the `forEach`'s lambda can be simplified further to: 

``` java Streaming lambdas are even shorter!
messages.stream().filter(m -> m.delay > 3000).forEach(System.out::println);
```

Because the parameter of the `forEach` lambda is simply consumed by the `println`, Java 8 now permits you to drop the parameter entirely. 

Earlier, I mentioned that streams permit you to chain lambdas -- in the case above, the `filter` method is an intermediate method, while the `forEach` is a terminal method. Other intermediate methods, that are immediately recognizable to functional programmers, are:  `map`, `flatMap`, and `reduce`, to name a few. 

To elaborate, I'd like to find all `Message`s that are delayed more than 3,000 seconds and sum up the total delay time. Without functional magic, I could write:

``` java Prosaic Java  
long totalWaitTime = 0;
for (Message message : messages) {
  if (message.delay > 3000) {
    totalWaitTime += message.delay;
  }
}
```

Nevertheless, with Java 8 and a bit of functional-foo, you can achieve a more elegant code construct like so: 

``` java Java 8 elegance
long totWaitTime = messages.stream().filter(m -> m.delay > 3000).mapToLong(m -> m.delay).sum();
```

Note how I am able to chain the `filter` and `mapToLong` methods, along with a terminal `sum`. Incidentally, the `sum` method requires a specific map style method that yields a collection of primitive types, such as `mapToLong`, `mapToInt`, etc. 

Functional style programming as a core language feature is an  astoundingly powerful construct. And while a lot of these techniques have been available in various 3rd party libraries like Guava and JVM languages like Scala and Groovy, having these features core to the language will surely reach a wider audience of developers and have the biggest impact to the developmental landscape.  

Java 8, without a doubt, drastically changes the [Java language](http://thediscoblog.com/blog/categories/java/) for the better. 

