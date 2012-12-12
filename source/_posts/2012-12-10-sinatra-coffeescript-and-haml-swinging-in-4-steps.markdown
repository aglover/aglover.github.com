---
layout: post
title: "Sinatra, CoffeeScript, and Haml: swinging in 4 steps"
date: 2012-12-10 21:49
comments: true
categories: [Ruby, CoffeeScript, JavaScript, Node]
---

I recently decided to ditch plain old JavaScript in a [Sinatra](http://www.sinatrarb.com/) based application in favor of [CoffeeScript](http://coffeescript.org/). The existing JavaScript wasn't terribly complex; however, I was looking at having to add some AJAX based long polling and I couldn't bring myself to do it in [JavaScript](http://www.ibm.com/developerworks/java/library/j-javadev2-18/index.html). Accordingly, I decided I'd use [CoffeeScript](http://www.ibm.com/developerworks/web/library/j-coffeescript/index.html), but, as it turns out, there were a few steps I had to put in place before I could enjoy some significant whitespace delimitation. 

First, I installed [Barista](https://github.com/Sutto/barista), which is a nifty Ruby gem that adds transparent CoffeeScript support to any Rack app. There were a few other gems available, however, I decided that Barista had the most mature documentation and the project still showed signs of life. 

Once I added the Barista gem to my Gemfile and ran [Bundler](http://gembundler.com/), I next had to require Barista in my Sinatra app; moreover, I had to register the Barista extension via `Sinatra.register Barista::Integration::Sinatra`. Note, that these steps need to be done after [Haml](http://haml.info/) is loaded. 

In order for Barista to work properly, you'll need to have a JavaScript runtime handy -- there are a number of options here, including [therubyracer](https://github.com/cowboyd/therubyracer); however, after a bit of research, I found some [concerning notes regarding therubyracer's memory consumption](http://stackoverflow.com/questions/6282307/execjs-and-could-not-find-a-javascript-runtime). After a bit of surfing, I happened upon [ExecJS](https://github.com/sstephenson/execjs), which states that Node will work just fine. Yeah! 

Accordingly, since this application runs on Ubuntu, I got to use a nifty script that I happened to have written about a year ago: [Ubuntu Equip](https://github.com/aglover/ubuntu-equip). In less than a minute, I had the latest and greatest version of Node running by running the command:

``` bash script for install Node on Ubuntu
wget --no-check-certificate https://github.com/aglover/ubuntu-equip/raw/master/equip_node.sh && bash equip_node.sh
```

Take note: this script sets a custom [apt-get](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) repository; otherwise, if you don't add this repository, you'll end up with an ancient version of Node. 

Finally, to actually start coding in CoffeeScript, all you have to do is use Haml's handy inline `:coffeescript` filter:

``` javascript CoffeeScript spinner
:coffeescript
  $(document).ready ->
    opts = { lines: 13, ...}
    spinner = new Spinner(opts).spin(document.getElementById('spinner'))
``` 

That's it! The steps required to get Sinatra, CoffeeScript, and Haml playing together are:

- Install Barista
- Configure Sinatra
- Install a JavaScript runtime engine
- Write some CoffeeScript

In short, Barista and [Node](http://www.ibm.com/developerworks/java/library/j-nodejs/) make Sinatra swing with CoffeeScript. 