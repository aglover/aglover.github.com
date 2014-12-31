---
layout: post
title: "Continuous Integration is a hack!"
date: 2014-12-30 15:21
comments: true
categories: [Java, TDD, Ruby, DevOps, Continuous Integration]
---

{% img left /images/mine/surprised-little-boy1.jpg %}"[Continuous Integration](http://www.amazon.com/gp/product/0321336380?ie=UTF8&tag=thdibl-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321336380) is a hack!" said my friend [Ben Rady](https://twitter.com/benrady) years ago during a discussion on CI hosted by [Stelligent](http://www.stelligent.com/). At the time, I was incredulous! How dare someone question the value of CI, especially when we had just finished writing a book about it! What's more, our book had been nominated for a prestigious [Jolt award](http://en.wikipedia.org/wiki/Jolt_Awards); indeed, [the following day, our CI book won it](http://www.drdobbs.com/tools/winners-of-the-18th-jolt-product-excelle/207600666?pgno=2)! 

In retrospect, Ben's point was poignant: CI is _reactionary_. You still have to wait some amount of time to ascertain correctness. That is, CI implicitly relies on a passive process to run a project's build and any corresponding tests. If those tests fail, you are of course, notified. Nevertheless, that notification is somewhat delayed: by the time a CI process runs the tests and reports on their status, you've already moved on to the next task. So much for failing fast! 

<!-- more -->

On the other hand, if tests, which are arguably the [quintessential proof of valid integration](http://www.ibm.com/developerworks/views/java/libraryview.jsp?search_by=code+quality:), are run _continuously_, then there's no wait time to ascertain issues! Ben's stance is really no surprise (especially if you know him) as he went on to play a major role in [Infinitest](https://infinitest.github.io/) and co-author [Continuous Testing: with Ruby, Rails, and JavaScript](http://www.amazon.com/gp/product/1934356700/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=1934356700&linkCode=as2&tag=thdibl-20&linkId=B53N2UWBWAKQNAVH). I've come to realize Ben's wisdom since then and have become a huge fan of Continuous Testing. 

<iframe style="float: right; margin-left: 1.5em; margin-top: 1.0em; width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=ss_til&ad_type=product_link&tracking_id=thdibl-20&marketplace=amazon&region=US&placement=1934356700&asins=1934356700&linkId=R2TMR5VSJAZVXCTS&show_border=true&link_opens_in_new_window=true">
</iframe> A more proactive means to ensuring all is kosher with a code base is to continuously run your tests as you work. In fact, an even more superior process would be to run any tests for code that has changed. That is, once a mapping has been established between code under test and a corresponding test, then when that code changes, the quickest, arguably most effective way to ensure that code isn't broken is to run its test(s). 

One efficient way to ascertain mapping is to name tests after the code they verify proceeded with a `test` moniker. For example, an `Account` object would have its corresponding test called `AccountTest` (or `AccountSpec`, etc). Any time the `Account` object changes, then the `AccountTest` would be run. 

[Continuous Testing](http://www.amazon.com/gp/product/1934356700/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=1934356700&linkCode=as2&tag=thdibl-20&linkId=FUVGSKMDG7GWI3CB) is an established practice in the world of Ruby. A great framework that facilitates it is called [Guard](http://www.javaworld.com/article/2074593/core-java/continuous-android-testing-with-guard.html). Briefly, [Guard](https://github.com/guard/guard) is a framework for watching a file system and sending out a notification upon some event (like a change).  With Guard, you can define specific file matching patterns and a corresponding action to take when an event is triggered. You can probably see that this linkage facilitates running tests continuously.

There's really no corollary framework or tool in Java (other than Infinittest and other IDE tools). Nevertheless, you can use Guard to continuously test a [Java](http://thediscoblog.com/blog/categories/java/) project. I wrote about [how to do it with an Android over two years ago](http://www.javaworld.com/article/2074593/core-java/continuous-android-testing-with-guard.html) and about 6 months ago I got involved in a Guard plugin for Gradle, dubbed [Guard::Gradle](https://github.com/bricker/guard-gradle).

To use Guard::Gradle, you'll need to have [Ruby](http://thediscoblog.com/blog/categories/ruby/) installed. For those on [OSX](http://thediscoblog.com/blog/categories/osx/), you already have Ruby. Once you have Ruby installed, the installation of Guard::Gradle couldn't be more easy: just open up a terminal in your desired Gradle project and type:

``` bash Simple installation!
$ curl https://raw.githubusercontent.com/bricker/guard-gradle/master/etc/installer.sh | bash -
```

The above command will download a script and execute it. That script will: 

 * Install Ruby's [Bundler](http://bundler.io/)
 * Install the Guard::Gradle plugin
 * Create a default `Guardfile`
 * Create a Guard launcher script, dubbed `guard.sh`

Therefore, after you run the above command, just execute `guard.sh` to start Guard::Gradle! Any time you change a file in your source tree, a [corresponding test](http://www.ibm.com/developerworks/java/library/j-cq10316/) will be executed using the Gradle `test` task. 

The default `Guardfile` assumes a standard Gradle project setup, with source code located in `src/main`. In fact, if you are  curious, take a look at the generated `Guardfile` and you'll see:

``` ruby Default Guard::Gradle Guardfile
guard :gradle do
  watch(%r{^src/main/(.+)\.*$}) { |m| m[1].split('.')[0].split('/')[-1] }
end
```

<iframe style="float: left; margin-right: 1.5em; margin-top: 0.7em; margin-bottom: 0.5em; width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=ss_til&ad_type=product_link&tracking_id=thdibl-20&marketplace=amazon&region=US&placement=0321336380&asins=0321336380&linkId=GQYZOKPUZCUVELWX&show_border=true&link_opens_in_new_window=true">
</iframe>

That `watch` command examines the `src/main` directory and attempts to execute a matched file's test. If no test is found, all tests are run. So either way, if a change happens, you're [covered with some amount of tests](http://www.ibm.com/developerworks/java/library/j-cq01316/index.html).
which ostensibly updates your local directory with all [upstream changes](http://thediscoblog.com/blog/categories/git/)). Consequently, if upstream changes have broken your local branch, you'll know instantly. No more need to remember to "run the tests" -- they're _always_ running. 

Of course, Continuous Integration isn't a hack. But Ben made a prescient point back then: if you want to fail fast and shorten the time to detect failures, why not do it at the source? Why not know instantly while you're working instead of some later time after you've moved on? Assuming your [project has tests](http://thediscoblog.com/blog/categories/tdd/), then Continuous Testing is the answer. Continuous Testing is the proactive yin to CI's reactive yang. 

Check out [Guard:Gradle](https://github.com/bricker/guard-gradle) today and know _instantly_ when you've made a breaking change! Now that's something everyone can dig, right? 






