---
layout: post
title: "A tale of three browsers"
date: 2012-09-24 15:39
comments: true
categories: mobile
---

I recently spent time evaluating various mobile browsers' HTML5 compatibility in an effort to understand the state of the mobile web. Along the way, I discovered that the good folks at Facebook, [who happen to have quite a lot of experience with HTML5](http://www.zdnet.com/facebooks-mark-zuckerberg-knocks-html5-in-favor-of-native-apps-7000004082/) and [mobility](http://gantdaily.com/2012/09/12/facebook-ceo-mark-zuckerberg-promises-better-mobile-strategy/) put together a handy website called [Ringmark](http://www.rng.io/). 

As the Facebook development team puts it, Ringmark

{% blockquote Facebook HTML5 Blog http://developers.facebook.com/html5/blog/post/2012/02/27/announcing-ringmark--a-mobile-browser-test-suite/ %}
helps you understand which mobile browsers support the functionality your app needs.
{% endblockquote %}

Thus, with Ringmark you can ascertain a particular browser's compatibility with HTML5 by simply going to [rng.io](http://www.rng.io/). 

Needless to say, I had some fun comparing Mobile Safari on my iPad to my Android 2.3.2 device. Interestingly enough, the fun really started when I decided to see what the feature functionality gap was between three browsers _on my desktop_. 
 
{% img center /images/mine/rng_io_3_browsers.png %}For the test, I used Chrome, Safari, and Firefox -- all three browsers are up to date as of the writing of this entry as well. The center browser is Chrome, left is Safari and right is Firefox. 

As you can see, all 3 browsers score _differently_ -- Chrome scoring best with only 29 failures in Ring 1 and Safari, the worst with over 40 failures. None even made it to Ring 2. 

If you think HTML5 poses some compatibility issues for mobile devices, you need to widen your scope: HTML5 support varies across _everything_. Indeed, the [Browser Wars](http://en.wikipedia.org/wiki/Browser_wars) are still being fought. The big difference now is that the wars are on multiple fronts.

