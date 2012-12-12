---
layout: post
title: "Comprehending the mobile development landscape"
date: 2012-12-02 18:11
comments: true
categories: [mobile, Java, iOS, Android, HTML5]
---

There's [no shortage](http://www.forbes.com/sites/parmyolson/2012/12/04/5-eye-opening-stats-that-show-the-world-is-going-mobile/) of [mobile growth](http://www.forbes.com/sites/connieguglielmo/2012/12/04/mary-meekers-must-read-interent-trends-report-says-android-phone-uptake-bests-apple/) statistics, but here's a few specific ones paint an overall picture of mobility:

* [Roughly 87% of the worlds population has a mobile device](http://www.digitalbuzzblog.com/infographic-2012-mobile-growth-statistics/)
* Earlier this year, [Facebook claimed an astounding 488 million](http://readwrite.com/2012/07/05/top-trends-of-2012-the-continuing-rapid-growth-of-mobile) mobile [monthly active users ](http://thediscoblog.com/blog/2012/11/28/the-essence-of-mobile-app-performance/)
* Android's user base is growing by [700,000 subscribers a day](http://www.digitalbuzzblog.com/infographic-2012-mobile-growth-statistics/) 

These three facts clearly point out that mobility is a growing, global phenomenon, and that it's drastically changing how people use the Internet. What's more, from a technology standpoint, mobile is where the growth is! 

But the mobile landscape is as varied as it is big. Unlike a few short years ago, when doing mobile work implied J2ME on a Blackberry, mobile development now encompasses [Android](http://www.android.com/), [iOS](http://en.wikipedia.org/wiki/IOS), [HTML5](http://en.wikipedia.org/wiki/HTML5), and even [Windows Phone](http://dev.windowsphone.com/en-us). That's 4 distinct platforms with different development platforms and languages -- and I haven't even mentioned the [myriad hybrid options](http://thediscoblog.com/blog/2012/09/01/cost-and-the-great-mobile-app-debate/) available! 

The key to understanding the mobile landscape is an appreciation for the various developmental platforms -- their strengths & weaknesses, speed of development, distribution, and, if you are looking at the consumer market, their payout.

##### Android

Android device distribution, as I pointed out earlier, is growing faster than other platforms, and the Android ecosystem has more than one app store: [Google Play](https://play.google.com/store?hl=en) and [Amazon's store](http://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011), just to name the two most popular ones. And by most accounts, [Google Play has as many](http://www.engadget.com/2012/06/27/google-play-hits-600000-apps/) or [more apps](http://news.cnet.com/8301-1035_3-57521252-94/can-apples-app-store-maintain-its-lead-over-google-play/) than Apple's App Store (careful with this statistic though, see details below regarding _payouts_). 

The massive adoption of Android, however, has lead to [fragmentation](http://opensignal.com/reports/fragmentation.php), which does present some significant challenges with respect to testing. In fact, the reality for most developers is that it is almost impossible to test an app on all combinations of device-OS version profiles in a cost effective manner (this is a growing service industry, by the way). 

On a positive note, Java, the native language of Android apps, is a fairly ubiquitous language -- some estimates [peg as many as 10 million active developers](http://en.wikipedia.org/wiki/Java_%28programming_language%29) so there's no shortage of able-bodied Java developers and their associated tools out there. 

Thus, with Android, you have a wide audience (both people with Android devices and developers to build apps) and multiple distribution channels. Yet, this large distribution of disparate devices does present some testing challenges; what's more, it can be more difficult to make money on the Android platform compared to iOS, as you'll see next.  

##### iOS

iOS, the OS for iPhones and iPads, has a [tight ecosystem](http://mobile.tutsplus.com/tutorials/iphone/understanding-the-ios-ecosystem/) and an avid user base, [willing to spend money](http://www.androidauthority.com/are-iphone-users-richer-better-educated-than-android-users-105032/), ultimately translating into [more money for developers](http://www.forbes.com/sites/darcytravlos/2012/08/22/five-reasons-why-google-android-versus-apple-ios-market-share-numbers-dont-matter/). That is, even though there are far more Android devices globally than iOS ones, the iTunes App Store generates more money than Google Play, which means more money for developers of popular apps. In many respects, users of iOS devices are also more willing to pay a fee for an app as opposed to Android ones. 

The development ecosystem for iOS has a higher barrier to entry when compared to something like Java or JavaScript. OSX is a requirement and the [cost alone](http://store.apple.com/us/browse/home/shop_mac/family/macbook_pro) here can be a barrier for a lot of developers; moreover, Objective-C can present some challenges for the faint of heart (manual memory management!). Yet, the tooling provided by Apple is almost universally lauded by the community at large (much like Microsoft's VisualStudio) -- [XCode](https://developer.apple.com/xcode/) is a slick development tool. 

While there isn't a lot of device [fragmentation on iOS](http://bgr.com/2012/06/12/apple-ios-fragmentation-iphone/), developers do have to deal with OS fragmentation. That is, there are only a handful of Apple devices but quite a lot of different versions living in the field at any given time due to a lagging factor of user upgrades. 

The iOS platform certainly offers a direct path to revenue, provided you can build a stellar app; however, compared to Android, this is a closed community, which has the tendency to rub some portion of developmental community wrong. Given you can quickly embrace Objective-C and afford the requisite software, iOS is almost always the first platform app developers target. 

##### HTML5

HTML5 is truly universal and its apps are available on all platforms without any need to port them -- JavaScript is as ubiquitous as Java; what's more, HTML itself has almost no barrier to entry, making HTML5 and JavaScript a force to content with when it comes to finding talented developers and mass distribution. Cost isn't even really part of the HTML5 equation too -- tools and frameworks are free. 

Yet, HTML5 apps suffer from a [distribution challenge](http://thediscoblog.com/blog/2012/09/07/past-performance-is-no-guarantee-of-future-results/) -- the major app stores do not carry these apps! Thus, in large part, as an HTML5 app developer, you are relying on a user to type in your URL into a browser. I for one, almost never type in a URL on my iPhone (while I will on my iPad). Lastly, [HTML5 is no where near parity with respect to UX](http://thediscoblog.com/blog/2012/09/25/modevtablet-2012-video-mobile-web-realities/) compared to native apps (and may _never be_). This, however, is only a disadvantage if you are building an app that requires a strong UX. There are plenty of great HTML5 apps out there! 

HTML5 offers an extremely low developmental barrier to entry and the widest support available -- all smart devices have browsers (note, [they aren't all created equal](http://thediscoblog.com/blog/2012/09/24/a-tale-of-three-browsers/)!); however, because there isn't a viable distribution channel, these apps have limited opportunity to make money. 

##### Windows Phone

Windows is [still unproven](http://www.forbes.com/sites/ericsavitz/2012/12/04/microsoft-dec-qtr-surface-sales-below-1m-units-analyst-says/) but could be an opportunity to get in early -- first movers in Apple's App Store without a doubt made far more money than if they had submitted the same apps today. In this case, you if want a truly native experience you'll build apps on the .NET platform (presumably C#). Windows machines are far cheaper than OSX ones, so there is little financial barrier other than license fees for VisualStudio and a developer fee for the Windows Phone Marketplace. 

Indeed, it appears that Microsoft is [modeling their app store](http://www.windowsphone.com/en-us/store) and corresponding policies off of Apple's -- thus there is a [tightly managed distribution channel](http://www.inquisitr.com/423599/windows-phone-8-app-downloads-improve-by-100/), presenting an opportunity to reach a wide audience and earn their money. But, at this point, the wide audience has yet to develop.

##### That's 4, but there's still more!

As I alluded to in the beginning of this post, there are 4 _primary platforms_ and myriad hybrid options, such as [PhoneGap](http://phonegap.com/) and [Appcelerator](http://www.appcelerator.com/), for example. These hybrid options have various advantages and disadvantages; however, the primary concerns one needs to think through are still speed of development, distribution, and payout.  

Before you embark on a mobile development effort, it pays to have the end in mind -- that is, before you code, have tangible answers for app distribution, development effort, and potential payout as these points will help guide you through the mobile landscape. 


