---
layout: post
title: "Running individual tests in Rails"
date: 2012-12-17 09:38
comments: true
categories: [Rails, Ruby]
---

Every now and again, I find myself struggling to remember how to execute a single test in [Rails](http://rubyonrails.org/). While I have [Guard](https://github.com/guard/guard) running the entire test suite _anytime_ a change is detected, there are times when I want to focus solely on one test. Accordingly, [Flavio Castelli has a great blog entry](http://flavio.castelli.name/2010/05/28/rails_execute_single_test/) detailing the manifold ways one can execute a single test using [Rake](http://rake.rubyforge.org/) and/or Ruby. 