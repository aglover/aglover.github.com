---
layout: post
title: "Painless Android swipe detection"
date: 2013-04-07 10:37
comments: true
categories: [mobile, Android, Java]
---

{% img left /images/mine/gesticulate.png %}Why bother building navigation buttons in an Android app when you can easily capture finger swipes? But, if you've ever implemented gesture section in Android there's the drudgery of implementing listeners and you also need to do some elementary Cartesian math. Save yourself the boilerplate bother mathematics and use a [library](https://github.com/aglover/gesticulate)! 

[Gesticulate](https://github.com/aglover/gesticulate) makes it painless to detect straightforward swiping motions like up, down, left, and right. It's a simple jar file you include in your Android `libs` directory. Throw Gesticulate's `SwipeDetector` inside an instance of Android's `SimpleOnGestureListener`, for example, and you're detecting swipes with ease! 

Gesticulate is used in Savvy Words, a flash card vocabulary app found on [Google Play](https://play.google.com/store/apps/details?id=com.b50.savvywords) and [Amazon's Appstore for Android](http://www.amazon.com/Beacon50-Savvy-Words/dp/B00C535D20/ref=sr_1_1?s=mobile-apps&ie=UTF8&qid=1365339189&sr=1-1). See the [Github project](https://github.com/aglover/gesticulate) for more details such as code examples for how to use Gesticulate, how to build it, and to see Gesticulate's tests. Swipe on, baby!
