---
layout: post
title: "Whiskered Node views"
date: 2013-08-13 12:00
comments: true
categories: [JavaScript, Node, CoffeeScript]
---


{% img right /images/mine/mustache_template.png %}As we draw closer to the glorious month of [Movember](http://us.movember.com/), I find myself pondering the [myriad template engines](http://paularmstrong.github.io/node-templates/) available for Node apps. The most popular is still probably [Jade](http://jade-lang.com/) as its syntax is [Haml](http://haml.info/)-like and results in quite clean views, lacking in HTMLish clutter.  

While Jade is handy, it takes some time to get used to. Plus, if you find yourself working with a UI person who prefers to speak in HTML, you'll find yourself translating between HTML and Jade (which isn't that hard with web apps like [HTML2Jade](http://html2jade.aaron-powell.com/), but nevertheless involves an extra _translation_ step). 

<!-- more -->

There are other template engines that map more closely to pure HTML. [Mustache](http://mustache.github.io/), for instance, forgoes reducing HTML entirely and introduces `{}`'s (i.e. mustaches) as a substitution delimiter. Thus, you can take normal HTML files and add some `{}`'s to make pages dynamic. 

In the world of Node, there are [a few](https://github.com/raycmorgan/Mu) Mustache implementations. One of the more interesting ones that I've used is [Whiskers](https://github.com/gsf/whiskers.js/). Whiskers is fairly lightweight and doesn't offer a lot of bells and whistles. As the project's README states

{% blockquote https://github.com/gsf/whiskers.js/ https://github.com/gsf/whiskers.js/ About Whiskers.js %}
Whiskers is focused on template readability. By limiting template logic, careful preparation of the context is encouraged, and the processing and formatting of data is kept separate from the design of the display.
{% endblockquote %}

Accordingly, you can do variable substitution, conditional logic, and looping out-of-the-box easily. But that's about all. 

To get started with Whiskers, you'll need to add it as a dependency to your project's NPM file like so:

``` json package.json NPM file
"whiskers" : "latest"
```

In this case, I'll always be grabbing the latest version. 

I prefer [CoffeeScript](http://localhost:4000/blog/categories/coffeescript/) when writing Node apps; consequently, the code examples I show you will be in [CoffeeScript](/blog/2012/12/10/sinatra-coffeescript-and-haml-swinging-in-4-steps/). Accordingly, in my `App.coffee` file, I need to then require Whiskers:

``` coffeescript Requiring whiskers in your Node app
whiskers = require 'whiskers'
```

You'll need to configure Express to leverage Whiskers; luckily, Express makes plugging in alternate template engines quite easy.

``` coffeescript Configuring a template engine with Express
app.set 'view engine', 'html'
app.engine 'html', whiskers.__express 
```

This indicates that your template files will end in `.html` and that for those file types, use the Whiskers framework. 

You can then render a Whiskers template like normal. For example, if I want to pass an `allWords` collection as the variable `words` to a template file dubbed `index.html`, I can do it like so:

``` coffeescript Rending a view
res.render 'index', {words: allWords}
```

In this case, ```allWords``` is an array full of `Word` classes. 

Inside my `index.html` file, I can access the `words` variable inside a bracketed `for` loop like so:

``` html Mustached HTML
<body>
  {for word in words}
    <div data-role="page" id="page_{word.id}" data-theme='c'>
      <div data-theme="g" data-role="header">
        <h3>
            Overheard Word
        </h3>
      </div>
     
      <div data-role="content">
        <div class="center-wrapper">
           <h2>{word.spelling} </h2>
           <p><em>{word.partOfSpeech}</em> - {word.definition}</p>
           <p>"{word.exampleSentence}"</p>
         </div>
      </div>
    </div>
  {/for}
</body>
```

Note inside the `for` loop, I have access to a `word` instance. I can call properties on it as well. Note, with Whiskers, you can't invoke methods on passed in objects. Only properties (i.e. `word.definition` isn't a function).

Jade certainly produces more elegant, less verbose view code. But Jade's whitespace delimiting coupled with the fact that basic HTML knowledge is near universal, make template frameworks like Whiskers, which permit normal HTML with `{}` delimiters appealing from time to time. 

