---
layout: post
title: "Crowd think often lacks clarity"
date: 2013-04-05 21:04
comments: true
categories: mobile
---


{% img right /images/mine/crowd.jpg %}In case you missed it, [InfoQ](http://www.infoq.com/) has an [interesting analysis regarding hybrid mobile app development frameworks](http://www.infoq.com/research/cross-platform-mobile-tools). They are profiling 12 tools and soliciting community feedback so as to make a [ThoughtWorks-like technology radar](http://www.thoughtworks.com/insights). If you participate in the voting you are entitled to see the voting distribution. 

What's interesting (and not too surprising) about this analysis is that frameworks like [PhoneGap](http://phonegap.com/) or [jQuery Mobile](http://jquerymobile.com/) (and laughably [JQTouch](http://jqtjs.com/)?) are leaning more towards _adoption ready_ when compared to frameworks like [Marmelade](http://www.madewithmarmalade.com/), [Coronona](http://www.coronalabs.com/) or [MoSync](http://www.mosync.com/). But this radar lacks some clarifying dimensions -- for example, if you want to produce a mobile website on a lot of different mobile platforms quickly, then PhoneGap is probably the way to go. But if you're looking to create a mobile app, then the choice to use PhoneGap must be treated with extreme caution. 

<!--more-->

You cannot create a [compelling user experience](http://thediscoblog.com/blog/2013/03/04/its-a-question-of-wow/) that works across [all platforms consistently](http://thediscoblog.com/blog/2012/09/01/cost-and-the-great-mobile-app-debate/) with this framework right now. And it isn't even the fault of PhoneGap entirely -- it's [HTML5](http://thediscoblog.com/blog/2013/02/17/circumventing-mobile-ux-expectations/). That's why going with something like JQTouch without viewing the big picture of what you are building is laughable. 

Frameworks like Marmelade or Corona might be a bit more difficult to get started with (after all, you code in something other than HTML or JavaScript) but they can produce a native app that offers a compelling user experience capable of generating a wow. Remember, native-ness [trumps the browser](http://thediscoblog.com/blog/2012/09/24/a-tale-of-three-browsers/) almost always unless your app is a series of screens that don't require the user to interact. 

Accordingly, the study lacks some needed dimensions -- mainly, whether or not you are building a mobile app or mobile website. Don't fall into the hype trap and jump into PhoneGap development unless you are sure what you are building is a simple mobile website and your audience isn't expecting something like Flipboard or AngryBirds. Otherwise, you'll find yourself investigating either pure play native development or frameworks that ultimately produce a native app. 
