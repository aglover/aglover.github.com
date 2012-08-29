---
layout: post
title: "Leaner JQuery with Zepto"
date: 2012-08-28 13:04
comments: true
categories: [JavaScript, mobile]
---

In a recent conversation with my friend [Jonathan Stark](http://jonathanstark.com/) we talked about how much has changed in mobile web development since [he and I chatted about cross-platform mobile development](http://www.ibm.com/developerworks/podcast/glover-stark-112911/index.html) in November of 2011. Of particular interest was how much adoption mobile web sites have achieved in less than a year -- yet, the adoption hasn't always been smooth. In fact, he pointed out a decidedly humorous site dubbed [WTF Mobile Web](http://wtfmobileweb.com/), which aims to educate the mobile web community via examples. 

One of the entries you can find on wtfmobileweb.com is an interesting Tweet, which I happened to see on [Jonthan](https://twitter.com/jonathanstark)'s timeline. [@philhawksworth](https://twitter.com/@philhawksworth) notes that [grolsch.com](http://grolsch.com/) isn't particularly mobile-savvy:

{% blockquote @philhawksworth https://twitter.com/philhawksworth/status/223805766161797121 %}
Dear web developers of the world. Can we stop this silliness before somebody gets hurt?! 
{% endblockquote %}

If you look closely in the image referenced by the tweet (or on wtfmobileweb.com), you'll see that slightly over 24MB of content is downloaded over the course of 388 requests. And that, presumably, a lot of that content is coming from everyone's friend [JQuery](http://jquery.com/). 

JQuery is an excellent library; however, it just _might_ contain a lot of unneeded code. And in the case of the mobile web, that translates into longer download times, which ultimately might yield a subpar user experience. Accordingly, if you still need a majority of JQuery's features but not its weight, you might want to give [Zepto](http://zeptojs.com/) a look.

As Zepto's website puts it:

{% blockquote http://zeptojs.com/ Zepto.com %}
Zepto is a minimalist JavaScript library for modern browsers with a largely jQuery-compatible API. If you use jQuery, you already know how to use Zepto.
{% endblockquote %}

Indeed, JQuery's `$` is there in all its glory -- only just _slimmer_. I was able to substitute in Zepto for JQuery in a [PhoneGap](https://github.com/aglover/caprice) project without any issues. Give it a shot -- unless you like having your website end up on wtfmobileweb.com! 