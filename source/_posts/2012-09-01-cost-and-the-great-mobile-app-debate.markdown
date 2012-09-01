---
layout: post
title: "Cost and the Great Mobile App Debate"
date: 2012-09-01 11:07
comments: true
categories: [mobile]
---


The Great Mobile App Debate rages on; in fact, it seems of late, the debate has been heating up _dramatically_. And interestingly enough, some interesting people are joining this lively discussion including [Martin Fowler](http://martinfowler.com/). In his presentation dubbed '[Developing Software for Multiple Devices](http://martinfowler.com/articles/multiMobile/)', Martin makes the case for [HTML5](http://en.wikipedia.org/wiki/HTML5) citing the reduced cost of building a single platform app as opposed to building an app for each platform. What's more, he notes that the option to build an app via some cross-platform framework (presumably he's talking about [Appcelerator](http://thinkmobile.appcelerator.com/blog/bid/211263/The-Great-Mobile-App-Debate-Native-vs-HTML5) or the [Xamarin](http://xamarin.com/)'s of the world) isn't viable. He invokes memories from the early days of desktop apps: 

{% blockquote Martin Fowler http://martinfowler.com/articles/multiMobile/#desktop-history  Developing Software for Multiple Devices %}
By applying the lessons of history we can infer that cross-platform toolkits will not be a viable solution to the multitude of mobile platforms.
{% endblockquote %}

And thus [ties this direction](http://martinfowler.com/articles/multiMobile/#uncanny) into a potential result of building apps that suffer from "[The Uncanny Valley](http://en.wikipedia.org/wiki/Uncanny_valley)" (i.e. "slight imperfections of an almost-native user interface cause a disproportionate negative response for the user").

This analogy, however, isn't necessarily correct. If you invoke memories of, say, Java and think back to AWT or Swing and the resulting, almost always, underwhelming UI/UX, then you've got a point. But the UIs of say, Xamarin are 100% native (which is the polar opposite of Swing!)-- there is no [Frankenstein-ness](http://martinfowler.com/articles/multiMobile/#frankenstein) here. There is no mimicking going on -- a single code base is compiled _natively_ into the underlying platform's UI. All you have to do is take a look at a sample application built using [Corona](http://www.coronalabs.com/products/corona-sdk/) and you'll realize that at this point, the Uncanny Valley argument for cross-platform frameworks is [FUD](http://en.wikipedia.org/wiki/Fear,_uncertainty_and_doubt). 

Which leads full circle back to the underlying argument for HTML5: cost. A cross-platform framework that produces a stellar app with exceptional UX _is cost effective_! You potentially build one app for multiple devices and it meets the terrifically high standards that consumers demand of a mobile app. And this is one thing that is often not met with pure play mobile web apps: they are not yet at parity with native-ness. More often than not, I find myself looking at [Frankenstein mobile web apps](http://wtfmobileweb.com/).

This isn't to say HTML5 isn't worth consideration. There are certainly apps that have been built as pure play web apps and they are terrific. The point of the discussion shouldn't focus on HTML5 versus Native versus Hybrid. It should instead focus on what you're trying to build. If you want to build a fantastic interactive app that wows users (think something like [Flipboard](http://flipboard.com/)) then you have a different line of thinking to pursue. If your goal is to build an information app that conveys data then UI/UX could be secondary and thus, an HTML5 option might just meet your needs. 

The Great Mobile App Debate shows no sign of abating anytime soon. HTML5 offers a plethora of promises for awesomeness. Someday this might be the case; however, HTML5 isn't an app development silver bullet. 

