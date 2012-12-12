---
layout: post
title: "The essence of mobile app performance"
date: 2012-11-28 16:59
comments: true
categories: mobile
---

{% img right /images/mine/binoculars.png %}Are you pumped that you just published an app? Yep, me too. But guess what? Once you've built, tested, and deployed a mobile app, you'll find yourself concerned with two factors: 

* visibility
* engagement

The view app stores like [iTunes](http://www.apple.com/itunes/) and [Google Play](https://play.google.com/store?hl=en) provide for you regarding app usage is fairly blurry. Downloads, the metric you get for free, doesn't really tell you anything about how your app is being used. 

While, at this point, there are essentially two platforms ([iOS](http://en.wikipedia.org/wiki/IOS) & [Android](http://en.wikipedia.org/wiki/Android_(operating_system))), it's a big big world out there with a [ton of different devices](http://www.androidpit.com/mobile-world-congress)! What's more, these devices, whether [Android](http://bgr.com/2012/05/16/android-fragmentation-visualized-opensignalmaps/) _or [iOS](http://bgr.com/2012/06/12/apple-ios-fragmentation-iphone/)_ are fragmented, which means your app will behave in some manner you haven't tested. Plus, the spectrum of varied users almost guarantees they are going to do something with your app that you've never even considered.  In an app world, you can't assume who will use your app!   

When's the last time you downloaded an app that has a measly one or two star rating? Me, neither. Once your app is live, you'll want to know what's happening. You'll want to know how your app is behaving and you'll want to know as quickly as possible before a landslide of negative feedback. 

Behavior can be ascertained in two ways: 

* logging
* events
 
Logging is a no-brainer -- but remember, crash logs are not enough sometimes! While you can get these in some form or another from app stores, oftentimes monitoring error and/or warning logs can bring to light that future crash. On that same note, don't over log either -- the loquacious logger usually ends up being a nuisance by flooding you with too much information. Think [signal-to-noise](http://en.wikipedia.org/wiki/Signal-to-noise_ratio) ratio here.

Events are like log messages, but they're more typed -- rather than describing geek-speak information like a JSON parsing warning at line 43, they capture succinct actions like a button click, a picture taken, a video download. The sky's the limit when it comes to what behavior you wish to capture with an event! 

Events come in a lot of flavors -- they can have a timer associated with them and, in some respects, they can also be categorized as a session. Either which way you spin it though, events tell you _how your app is being used_. Combine these tools with dimension data like device information and geo-location and you start to see what's happening with your app in real time -- your view is certainly less cloudy!  

Another question: are you giving your app away for free _without any monetization strategy_?  I didn't think so. You wouldn't have read this far otherwise. [Engagement](http://www.insidefacebook.com/2009/01/12/application-insights-how-exactly-is-mau-calculated/) is about understanding the lifetime value of a user. You want some sort of return on your investment (and if it isn't money, then it is certainly engaged eyeballs), otherwise you wasted your time building the app. 

When's the last time you downloaded an app, launched it, decided it was terrible and uninstalled it immediately? Yeah, me too (it happened while I was writing this post). I recently spoke with a government agency that disclosed to me that they had over 500,000 downloads of their app. That's great news! Unfortunately, they had no idea if the app was being used. I bet the app I just deleted is happy they had another download too. 

What everyone wants to know, in addition to _how_ their app is being used, is _whether it is being used at all_. Are people engaged in actively using your app or do they [abandon](http://www.nuance.com/ucmprod/groups/enterprise/@web-enus/documents/collateral/nc_020218.pdf) it? A telling metric here is _active users_ -- this accurately discloses how often someone uses your app. And if your monetization strategy relies on ads, for example, this metric means everything to you. 

Just like with visibility, if you add dimension data like device information, geo-location, and even duration, a fairly vivid picture of user engagement is painted for you. You can ascertain if your app is actually delivering value. 

Building, testing, and deploying your app is no easy feat. Congrats on getting that far! But don't forget about what happens after that effort finishes. Putting an app out into the wild without some sort of monitoring is like driving your car from the back seat while sitting backwards. All you see is outdated and you have no view as to where you're headed.  Visibility and engagement. If they don't matter to you now, they will. 

