---
layout: post
title: "Mobile-isticly optimized in 10 seconds"
date: 2013-05-15 13:20
comments: true
categories: mobile
---

{% img right /images/mine/unoptmobile.png %}Anyone ever told you that your website isn't mobile optimized? Or have you ever seen a lilliputian-looking website on your device? You know, one that renders so small you are forced to squint as you enlarge various parts of the site with your fingers just to read it? 

Websites render this way on mobile devices because they lack a simple `meta` tag. While the subject of mobile website optimization can be rather involved (especially when dealing with [CSS media queries](http://css-tricks.com/css-media-queries/), which take longer than 10 seconds to understand), there is a simple trick that can at least can make your website render normally on a mobile device. And it can be done in 10 seconds. 

<!--more-->

Simply add the following `meta` tag in the `head` element of your website's index page: 

``` html viewport definition
<meta name="viewport" content="width=device-width, initial-scale=1">
```

The viewport `meta` tag is supported by browsers on both iOS, Android, and by other device browsers including Internet Explorer Mobile on Windows Phone 8. This tag instructs a browser on how to properly display a webpage; without it, a webpage is, unfortunately, displayed mini-style on device browsers, which have a narrow width.

Thus, the viewport tag essentially zooms in the display of a webpage. In the case of the example tag above, the width of the website is set to the device's width and the scale is set to 100% -- this'll allow the website to be displayed normally on a mobile device. Website visitors won't have to squint or pinch and expand to just to read the site's relevant text. 

[Paulund.co.uk][0] has a really [good write up][1] regarding the usage of the viewport `meta` tag as well as CSS media queries; what's more, the good folks who created [HTML5boilerplate.com][2] have a [nifty presentation][3] that's worth a read too.

[0]: http://www.paulund.co.uk
[1]: http://www.paulund.co.uk/understanding-the-viewport-meta-tag
[2]: http://html5boilerplate.com/mobile/
[3]: http://t.co/dKP3o1e