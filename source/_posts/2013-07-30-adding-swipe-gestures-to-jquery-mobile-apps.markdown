---
layout: post
title: "Swipe gestures in jQuery Mobile apps"
date: 2013-07-30 12:52
comments: true
categories: [mobile, JavaScript, HTML5, jQuery Mobile]
---


{% img left /images/mine/swipe-right.png %}I find that [swipe gestures](http://thediscoblog.com/blog/2013/04/07/painless-android-swipe-detection/) for navigating between screens in a mobile app [quite nice](http://thediscoblog.com/blog/2013/07/01/mobile-for-the-masses-words-and-gestures-with-overheard-word/). [Early on](http://www.ibm.com/developerworks/library/j-mobileforthemasses2/) in my mobile development journey, I found myself instinctively adding navigation buttons, but quickly found them cumbersome for users to tap; plus, those buttons took up precious screen real estate! Gestures, on the other hand, free up screen space by removing needless buttons and give users a more interactive experience. 

Implementing right and left swipes in an [jQuery Mobile](http://jquerymobile.com/) app is fairly straightforward, but there are a few gotchas that I was able to piece together via multiple [stackoverflow threads](http://stackoverflow.com/questions/7533772/how-to-swipe-between-several-jquery-mobile-pages), blogs, and finally [jQuery Mobile's own documentation](http://api.jquerymobile.com/). 

<!-- more -->

In a jQuery Mobile app, you [define pages](http://the-jquerymobile-tutorial.org/jquery-mobile-tutorial-CH02.php) within `div` tags that represent a UI screen -- you can then declare transitions between pages well -- slide, flip, etc.  The key aspect with swiping between page `div`s is the selector for them, which is `div[data-role='page']`. 

Once you have a handle to that `div`, you can proceed forward with a left swipe via jQuery's [`next`](http://api.jquery.com/next/) function. Conversely, swiping right with the intent of going back is facilitated by finding the previous matching `div[data-role='page']` via jQuery's [`prev`](http://api.jquery.com/prev/) function. 

Also note, going backwards via a swipe requires that you _reverse_ the slide transition, otherwise it looks misleading to the user (i.e. the transition is from left to right rather than the other way around!). 

Accordingly, the JavaScript for swipe gestures should be placed within a [`pageinit` event](http://jquerymobile.com/demos/1.2.0/docs/api/events.html) like so:

``` javascript Enabling swipe between page divs
$(document).on('pageinit', function(event){
  $('div.ui-page').on("swipeleft", function () {
    var nextpage = $(this).next('div[data-role="page"]');
      if (nextpage.length > 0) {
        $.mobile.changePage(nextpage, "slide", false, true);
      }
  });

  $('div.ui-page').on("swiperight", function () {
    var prevpage = $(this).prev('div[data-role="page"]');
    if (prevpage.length > 0) {
      $.mobile.changePage(prevpage, { transition: "slide", reverse: true }, true, true);
    }
  });
});
```

Note, this script should be referenced in your DOM _before_ you pull in the jQuery mobile js file. That is, put the code above where you add jQuery mobile in your document's header (but _after_ you load jQuery itself):

``` html Including jQuery Mobile
<script src="https://d10ajoocuyu32n.cloudfront.net/jquery-1.9.1.min.js"></script>
<!-- add pageinit swipe initialization here -->
<script src="https://d10ajoocuyu32n.cloudfront.net/mobile/1.3.1/jquery.mobile-1.3.1.min.js"></script>
```

Once you've done that, you'll be able to swipe between page `div`s. 

