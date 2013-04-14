---
layout: post
title: "Hybrid inappropriateness"
date: 2013-04-14 11:14
comments: true
categories: mobile
---

{% img right /images/mine/confused.png %}The mobile development landscape is [bursting at the seams][4] with frameworks and tools that enable you to target [two primary mobile platforms](http://thediscoblog.com/blog/2013/03/25/modeveast-2012-panel-discussion/): iOS and [Android](http://thediscoblog.com/blog/categories/android/). And the mobile community has [classified these frameworks and tools](http://thediscoblog.com/blog/2012/12/02/comprehending-the-mobile-development-landscape/) into three categories: native, hybrid, and web. Unfortunately, limiting the classification to three [is inadequate](http://thediscoblog.com/blog/2013/04/05/crowd-think-often-lacks-clarity/) as the term hybrid is too generic. 

Web apps are truly ubiquitous because they take advantage of the browser and are thus, cross platform. You, for the most part, can get away with [maintaining one code base](http://thediscoblog.com/blog/2012/09/01/cost-and-the-great-mobile-app-debate/) that can be viewed on iOS, Android, and the other minor players. Yet, the key point of Web apps is that they are [_web sites_, not mobile apps](http://thediscoblog.com/blog/2012/09/25/modevtablet-2012-video-mobile-web-realities/). 

As you begin to blur that mobile app/mobile website line, you'll [stumble over various issues](http://thediscoblog.com/blog/2013/03/04/its-a-question-of-wow/) like performance or varying behaviors depending on the underlying device.  Worse, you'll find that [not all browsers are created equally](http://thediscoblog.com/blog/2012/09/24/a-tale-of-three-browsers/) and thus, you'll need to [compensate for various scenarios](http://thediscoblog.com/blog/2013/02/17/circumventing-mobile-ux-expectations/) or use assorted compensating tools like [Modernizr][1]. Or, you'll find yourself throwing the web app into a container because you want mass distribution via an app store or because you want to take advantage of some native feature. 

And at that point, in the eyes of many, you've built a hybrid mobile app. But that's not the whole story.

Hybrid frameworks aren't all created equal.  There are, in fact, two flavors of hybrid-ness: _hybrid-web_ and _hybrid-native_. Both are extremely different; in fact, to showcase the difference, I need only compare the "featured" apps of two hybrid frameworks: [PhoneGap][0] and [Marmalade][m1]. 

PhoneGap is a great framework for building hybrid-web apps. You code in HTML and JavaScript and the resultant code is run inside of a web view managed by a native container.  You ostensibly write one code base and deploy to all the primary mobile platforms. Similar frameworks are: [IBM's Worklight][3] and [Icenium][2]. [Sencha Touch](http://www.sencha.com/products/touch) falls into this realm as well when you employ their container technology. 

{% img center /images/mine/phonegap_example.png %}

[Marmalade][m1] is a hybrid-native app platform; that is, you can code in an intermediary language like C++ that is ultimately compiled down into native code (i.e. Java for Android, Objective-C for iOS, etc). These apps are not characterized by a web view in any way. They are native, close to the bone as it gets apps. The benefit is that you only had to write _one code base_, the underlying tool took care of the dirty work in making it work similarly on iOS and Android, for example. Similar frameworks are: [Corona](http://www.coronalabs.com/), [MoSync](http://www.mosync.com/), and [Appcelerator](http://www.appcelerator.com/) to name a few.

{% img center /images/mine/marmalate_example.png %}

Now look closely at the [featured apps](http://phonegap.com/app/feature/) of each [hybrid framework](http://www.madewithmarmalade.com/app-showcase). They're [distinctly](http://phonegap.com/blog/2011/11/07/phonegap-application-craft-pain-free-mobile-app-development/) [different][m2] aren't they? 

PhoneGap and the rest of the hybrid-web frameworks are good at producing informational apps. These apps can look beautiful and certainly can have some compelling features like tight integration with social networks and geo-tracking, for example. But they're inadequate for games and still lack a rich user experience when compared to more native apps like Flipboard or AngryBirds. 

Marmalade and the rest of the hybrid-native crowd shine when it comes to highly interactive apps like games; what's more, these frameworks can build an app that generates a wow. And therein lies the crucial aspect that's missed when the industry blindly assumes all hybrid options are similar. They couldn't be more incorrect -- thus, we need at least four categories (native, web, hybrid-native, and hybrid-web) and we need to eschew the lone term "hybrid" as it lacks clarification and therefore has no meaning.

[0]: http://phonegap.com/
[1]: http://modernizr.com/
[2]: http://www.icenium.com/
[3]: http://www-01.ibm.com/software/mobile-solutions/worklight/
[m1]: http://www.madewithmarmalade.com/
[m2]: http://www.madewithmarmalade.com/marmaladesdk/application/cut-rope
[4]: http://skytechgeek.com/2011/09/10-mobile-application-frameworks-for-easy-development/
