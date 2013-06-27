---
layout: post
title: "Android tip: ListView becomes black when scrolling"
date: 2013-06-27 13:15
comments: true
categories: [mobile, Android, Java]
---

One of [my Android apps](https://play.google.com/store/apps/details?id=com.b50.hoproll) recently suffered from a nefariously ugly UI glitch that, embarrassingly, a user reported to me.  Strangely, this phenomenon did not surface itself in local testing on either emulators or devices when I first wrote the app; nevertheless, I was able to reproduce the error after the bug report came in. I suspect that recent updates in the Android platform might have exacerbated the issue for my app as I'm fairly certain I never saw it before. 

[HopRoll](http://www.amazon.com/Beacon50-Hop-Roll/dp/B00A9Z5P42/ref=sr_1_1?ie=UTF8&qid=1370959609&sr=8-1&keywords=hoproll) displays a ListView of hops and has a custom background like so: 

{% img center /images/mine/hop_roll_2.png %}

When people scrolled through that list, however, they would see a nasty black partially rendered screen that made the app completely unusable as you can see below:

{% img center /images/mine/hop_roll_1.png %}


If you start to notice that your [ListView](http://developer.android.com/reference/android/widget/ListView.html) containing custom backgrounds become [horribly black](http://android-developers.blogspot.com/2009/01/why-is-my-list-black-android.html) when scrolling then you've got the same issue. 

Luckily, it's quite easy to rectify. It turns out that when scrolling, there is some rendering magic going on that can obstruct the background in an ungainly way; you [can fix it](http://stackoverflow.com/questions/2833057/background-listview-becomes-black-when-scrolling) by adding one line to the ListView's layout definition:

``` xml Adding this to your ListView Layout definition will fix the issue
android:cacheColorHint="@android:color/transparent"
```

With that line in place, scrolling, once again, becomes clean and smooth. 

You can find my app, Hop Roll, which is your go-to home brew resource for all hop related information on [Google Play](https://play.google.com/store/apps/details?id=com.b50.hoproll) and [Amazon's Android App Store](http://www.amazon.com/Beacon50-Hop-Roll/dp/B00A9Z5P42/ref=sr_1_1?ie=UTF8&qid=1370959609&sr=8-1&keywords=hoproll) -- keep on trucking, baby! 
