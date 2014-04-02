---
layout: post
title: "Schooled in ping pong"
date: 2014-04-01 20:53
comments: true
categories: [CoffeScript, TDD]
---


{% img right /images/mine/pingpong.jpg %}Years ago, a [good friend of mine][1] taught me an effective pair programming technique that results in universally covered code. What's more, this manner of pairing ultimately made me a better developer as I learned myriad different coding skills from my coding partner, ranging from testing techniques, defensive coding, and encapsulation, just to name a few. 

A lot of my coding practices today can be traced to tactics I learned from playing what's known as _ping pong_. 

<!-- more -->

What's [ping pong][3] you ask? It's a pair programming approach that's absurdly simple: 

  1. You write a failing test.
  1. Your crony writes just enough code to make that test pass.
  1. Your crony writes an additional failing test.
  1. You write just enough code to make that new failing test pass.
  1. Repeat (and refactor as needed). 

It's that easy. The beauty of this process is that by limiting your coding to _just enough logic_ to make a test pass, followed up with an additional test that _must fail_ (which is thereby testing something new) you end up with 100% code coverage. What's more, you don't end up writing a lot of superfluous code. 

#### Show me the money

Let me show you how it works. My fictitious ping pong pairing, with Frank and Glenn, will focus on creating a calculator that can add 2 numbers in [CoffeeScript][4]. 

Step 1 is to write a failing test. Accordingly, Frank crafts a test in [Mocha][5] with the expectation that 1 and 4 make 5. 

``` javascript Step 1: a failing test
require 'should'
require 'mocha'

describe 'a calculator', ->
    describe 'the addition function', ->
        it 'should support adding 2 positive numbers', ->
        	calculator = new Calculator
        	value = calculator.add(1, 4)
        	value.should.equal 5
```

Glenn runs the test and is immediately presented with an error: 

``` bash Oh no! 
1) a calculator the addition function should support adding 2 positive numbers:
   ReferenceError: Calculator is not defined
```     

"No problem" thinks Glenn and he adds the following line of code to the top of the test: 

``` javascript Import it, baby
Calculator = require 'Calculator'
```

Glenn knew this wouldn't make the test pass, but he recognized that the error he saw was related to the fact that `Calculator` wasn't defined in the test. One problem has been licked, but he knows another one exists. Nevertheless, he runs the test again. 

``` bash Still erroring out?
Error: Cannot find module 'Calculator'
```

Glenn's one step closer to making the test pass -- he must define a `Calculator` class. Here, Glenn decides to write the _absolute minimum amount of code_ to make the test pass:

``` javascript As easy as it gets
class Calculator
	add: (x, y) ->
		5

module.exports = Calculator
```

Glenn now proceeds to run the test. 

``` bash And it passes!
a calculator
    the addition function
      ✓
```

Step 2 is now complete: Glenn has written the minimum amount of code to make Frank's test pass. Now Glenn must write a new test that fails. 

Accordingly, for step 3, Glenn decides to do a bit of refactoring of the test and adds the following logic: 

``` javascript Step 3: A newly failing test
require 'should'
require 'mocha'
Calculator = require 'Calculator'

describe 'a calculator', ->
    describe 'the addition function', ->
        it 'should support adding 2 positive numbers', ->
          inputs = [{x: 1, y: 2, ans: 3}, {x: 1, y: 4, ans: 5}]
          calculator = new Calculator
          for input in inputs
            calculator.add(input['x'], input['y']).should.equal input['ans']
```

Frank runs the test and, indeed, the two intrepid developers are presented with a new error:

``` bash Without a surprise, it fails
a calculator
    the addition function
      1) should support adding 2 positive numbers


  0 passing (5ms)
  1 failing

  1) a calculator the addition function should support adding 2 positive numbers:
     AssertionError: expected 5 to equal 3
```

But Frank's no fink. He quickly corrects the state of failing code with a simple change to the `Calculator`. 

``` javascript Addition at its finest
class Calculator
  add: (x, y) ->
    x + y

module.exports = Calculator
```

He reruns the test and, not surprisingly, everything is kosher. Now it's Frank's turn for a bit of sadism, so he refactors the test slightly but throwing in a random `String`. 

``` javascript Tricky, Frank. Tricky.
require 'should'
require 'mocha'
Calculator = require 'Calculator'

describe 'a calculator', ->
    describe 'the addition function', ->
        it 'should support adding 2 positive numbers', ->
          inputs = [{x: 1, y: 2, ans: 3}, {x: 1, y: 4, ans: 5}, {x: '1', y: 4, ans: 5}]
          calculator = new Calculator
          for input in inputs
            calculator.add(input['x'], input['y']).should.equal input['ans']
```            

Naturally, the test fails with the not too strange result of `AssertionError: expected '14' to equal 5`. 

Glenn, feeling fiendish, goes to work by enhancing the `Calculator` _ever so slightly_. 

``` javascript Why just x?
class Calculator
  add: (x, y) ->
    new Number(x) + y

module.exports = Calculator
```

The addition of the `Number` type for _x only_ fixes Frank's tricky test. Time to write a failing test. In what should be no big surprise, Glenn simply adds an additional associative array which contains _two_ `String`s. 

This step might seem pedestrian, but it's deliberate: by only writing the minimum amount of code required to make a test pass and following that step up with an additional test, Glenn's villainous coding behavior is ensuring every logical path through the evolving code is covered by a test. 

Naturally, once Glenn's new associative array is forcing a failure, Frank updates the `add` method to make the `y` input parameter a `Number` as well. Frank then proceeds to add a few more values to the `inputs` array. 

``` javascript Let's see if you can make it fail now!
require 'should'
require 'mocha'
Calculator = require 'Calculator'

describe 'a calculator', ->
    describe 'the addition function', ->
        it 'should support adding 2 positive numbers', ->
          inputs = [{x: 1, y: 2, ans: 3}, 
             {x: 1, y: 4, ans: 5}, 
             {x: '1', y: 4, ans: 5},
             {x: '1', y: '4', ans: 5},
             {x: -1, y: 4, ans: 3},
             {x: 1.5, y: 4, ans: 5.5}]
          calculator = new Calculator
          for input in inputs
            calculator.add(input['x'], input['y']).should.equal input['ans']  
```            

He runs the test hoping for a failure, but to no avail. Time to think a bit harder. Time to embrace one's inner sadistic troll. He then adds the following associative array to the `inputs` array: `{x: 1.5, y: null, ans: 1.5}`. Yet, the test still passes. 

It's at this point that both Frank and Glenn feel they're done: `add` works to their expectations. If they wanted to go on, Frank could proceed to write a failing test for a `subtract` function and so on.  

#### It's a learning experience

Interestingly, Frank learned a new way to approach testing from Glenn. When Glenn introduced the array of associative arrays, Frank was initially surprised. He figured it would be easier to write an additional test case. He even pointed that out to Glenn.  But Glenn countered that more `it` phrases don't necessarily mean better tests; and besides, using his method of iterating over a series of values provided them with a lot of flexibility _without_ a lot of boiler plate code -- something Frank agreed with. Accordingly, Frank learned a new way of thinking about testing -- make a single test a bit more powerful by using a series of values rather than making a lot of similar tests. 

Frank also learned a bit of coding patience. He was initially annoyed when Glenn only updated `x` to use a `Number` type and completely ignored `y`. He accosted Glenn: "Update `y` too?!" to which Glenn countered that the only amount of code he needed to write to make Frank's test pass. Frank then saw that his tricky test case with only 1 `String` input was essentially inadequate. 

#### Benefits and costs

Ping pong pairing is a methodically deliberate manner of programming that forces you to be extremely patient, because you are limited to write the least amount of code to make a test pass _and no more_. And while it might seem like this process is a slow way to get things done, the benefits far outweigh the costs. 

For one thing, two chums practicing ping pong will produce well tested functionality. Second, they'll produce this functionality in a lot fewer lines of code than if they had done it otherwise. That's a good thing and I've seen this result consistently. There's one thing that I've come to learn: less working code is better than more. And pay attention here: producing fewer lines of working code usually means you have more tests than the code those tests are validating. 

Additionally, this technique forces developers to learn from each other. Every developer has a great coding facility up their sleeves and every developer can learn something _new_.  

Finally, ping pong results in two parties having an intimate knowledge of code. Two is better than one, in my book. 

#### Ping pong style

I've found that ping pong is best practiced [Pomodoro style][2] -- ping pong isn't something you can do all day, nor would I recommend you do so. Do it for 20 to 25 minutes then take a break and figure out if it was valuable or not. Chances are you'll find it was a worthwhile investment in time and you'll elect to do it again later in the day or another time.  

Make no mistake: holding back the urge to loquaciously code away takes a lot of discipline and practice. But ping pong _will make you a better programmer_ even if you only do it once in a while. 

[1]: http://www.pauljulius.com/
[2]: http://en.wikipedia.org/wiki/Pomodoro_Technique
[3]: http://c2.com/cgi/wiki?PairProgrammingPingPongPattern
[4]: http://thediscoblog.com/blog/categories/coffeescript/
[5]: http://visionmedia.github.io/mocha/