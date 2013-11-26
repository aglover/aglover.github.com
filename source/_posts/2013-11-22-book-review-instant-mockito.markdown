---
layout: post
title: "Book review: Instant Mockito"
date: 2013-11-22 13:33
comments: true
categories: [Java, TDD]
---

Recently, the good folks over at [Packt Publishing](http://www.packtpub.com/) gave me a copy of their newly published [_Instant Mockito_](http://www.amazon.com/gp/product/B00ESX15M2/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00ESX15M2&linkCode=as2&tag=thdibl-20), by [Marcin Grzejszczak](http://toomuchcoding.blogspot.com/). Packt's [Instant series](http://www.packtpub.com/books/instant) are really enjoyable. The premise of these books is that they're short and sweet. They're slightly more than a tutorial; they get you up and running quickly while throwing in a few more facets that go beyond the typical tutorial. 

<!-- more --> 


<iframe style="float: right; margin-left: 1.5em; height:260px; width:150px;" src="http://rcm-na.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=thdibl-20&o=1&p=8&l=as4&m=amazon&f=ifr&ref=ss_til&asins=B00ESX15M2" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>

[_Instant Mockito_](http://www.amazon.com/gp/product/B00ESX15M2/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00ESX15M2&linkCode=as2&tag=thdibl-20) is a quick read at 92 pages; what's more, Marcin does a great job of keeping a consistent example domain alive throughout the entire book. The big picture is how to use [Mockito](http://thediscoblog.com/blog/2013/08/01/imbibing-the-fluency-of-mockito/) with [JUnit](http://thediscoblog.com/blog/2013/08/04/sometimes-tdd-requires-a-hammer/), testing a fictitious restaurant ordering system. 

The format of Packt's Instant books are all similar — the first part is a quick "up and running" that essentially encapsulates the basic tutorial you can find on a project's home page. The meat of an Instant book comes next, where the author dives into a number of features they feel are important to grasp a particular subject. 

In the case of [Marcin's book](http://www.packtpub.com/how-to-create-stubs-mocks-spies-using-mockito/book), he elaborates on 8 key Mockito subjects: 

  * Performing argument matching
  * Stubbing multiple calls
  * Working with void methods and thrown exceptions
  * Stubbing with a custom answer
  * Verifying behavior (including argument capturing, verifying call order, and working with asynchronous code)
  * Doing partial mocking (spying)
  * Reducing boilerplate code with annotations
  * Taking advantage of advanced mocks configuration
 
Again, each subject expands upon a waiter-taking-an-order domain. As this is a book focusing on Mockito, the mocking aspects are straightforward — this isn't a book on how to test poorly written code, for example, so the code under test in this case uses dependency injection. This makes it easy to grasp Mockito subjects without having to delve into the intricacies of terrible code (ironically, I often times find myself employing Mockito when dealing with poorly written code). 

Speaking of poorly written code: the only thing I did not particularly enjoy about the book was the formatting of the code. Code formatting has always been problematic in books, however, many publishers have figured out how to properly convey code (in particular, [O'Reilly](http://www.oreilly.com/) books do a good job). 

In the case of [_Instant Mockito_](http://www.amazon.com/gp/product/B00ESX15M2/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00ESX15M2&linkCode=as2&tag=thdibl-20), the code examples (at least reading the epub version on my iPad) have such narrow margins that each logical line of code is often broken into 3 or more lines (and dashes are used to signify a line break). While it certainly doesn't render the code unreadable, it makes instant code comprehension challenging. I found myself having to read through each code example multiple times just to get the basic idea. 

Code formatting aside, [Marcin's book was an easy read](http://www.packtpub.com/how-to-create-stubs-mocks-spies-using-mockito/book) and the 8 features he feels every Mockito user should know are well explained.  As a user of Mockito, I couldn't agree more with the relevance of these features. If you enjoy quick reads, want to learn the basics of mocking in JUnit with Mockito, and you can put up with inadequate code formatting, then go ahead and give [_Instant Mockito_](http://www.amazon.com/gp/product/B00ESX15M2/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00ESX15M2&linkCode=as2&tag=thdibl-20) a read! 