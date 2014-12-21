---
layout: post
title: "Batten down those Node dependency hatches"
date: 2014-12-20 19:18
comments: true
categories: [JavaScript, Node]
---


[Dependency management](http://en.wikipedia.org/wiki/Dependency_%28project_management%29) is oftentimes a mundane subject. And it's not much of a subject at all if you don't depend on rapidly changing libraries. Of course, you might not always _realize_ you're depending on a rapidly changing library -- especially, if you happen to take a rather liberal approach of depending on snapshots or latest versions, as I often do. 

I recently found a nifty [npm](https://www.npmjs.com/) utility for [Node](http://thediscoblog.com/blog/categories/node/) applications that helped me avoid some rather annoying dependency related issues. It's quite similar to Ruby [Bundler](http://bundler.io/)'s `Gemfile.lock` files, but in the case of npm, you'll need to actually run an additional command.

<!-- more --> 

Take, for example, the following snippet from a [Hubot](https://github.com/hubot-scripts/hubot-heroku-dynos) npm `pacakge.json` file, which specifies the project's dependencies.  

``` json package.json NPM file
"dependencies": {
    "hubot": ">= 2.6.0 < 3.0.0",
    "hubot-scripts": ">= 2.5.0 < 3.0.0",
    "hubot-hipchat": "^2.6.4-2",
    "underscore": "1.3.3",
    "underscore.string": "2.1.1",
    "githubot": "0.4.x",
    "moment": "latest",
    "cheerio":"latest",
    "date-utils": ">=1.2.5"
  }
```

As you can see, npm has a powerful version syntax (like almost all other dependency management packages) that allows you to target a particular version (like [underscore](http://underscorejs.org/) version 1.3.3) or range of versions (like [hubot](https://hubot.github.com/) which can be anything greater than or equal to version 2.6.0, but less than 3.0.0). 

Take special note though on the versions targeted for cheerio and moment: they're both pointing to _latest_, which means the tip. And as you've probably guessed, latest today could be a different version from latest tomorrow. 

Accordingly, if someone else runs `npm install` later, they could get a different version of moment or cheerio. And this could lead to unexpected behavior and/or failures. You could, naturally, update your `package.json` file to point to a _particular version_ but that's painful, as it forces you to figure out which ones you want. And of course, when a new version is released, you'll then have to manually update your `package.json` file. 

Explicitly pointing to a particular version is prudent  -- it frequently makes a lot of sense to rely on known, stable versions of frameworks. In the case of Hubot, for example, that dependency shouldn't change much, nor do I plan to keep up to date with the tip. But the moment dependency, that's another story -- I want the tip and I am totally willing to get a new version at some point in the future. 

Nevertheless, I want everyone who collaborates on my project to have the exact same version -- and that version should be one I decide on. 

Enter npm's `shrinkwrap` command.  This creates a _lock file_ dubbed `npm-shrinkwrap.json`. This lock file points to exact versions and it becomes the source of truth for all versions of required dependencies (even those pulled in by your dependencies' dependencies). 

All you have to do is run, via the command line, `npm shrinkwrap` and check this file into your SCM. The next time someone runs an `npm install` (or update) the shrinkwrap file is consulted by npm and _only_ those precise versions are incorporated into your project. 

Now anyone who runs `npm install` will get the exact versions you intend. In fact, if you or anyone else changes the `package.json` file, nothing will happen unless you delete the shrinkwrap file and then run `npm install`.

Much like Bundler's `Gemfile.lock` files, [npm's shrinkwrap](http://blog.nodejs.org/2012/02/27/managing-node-js-dependencies-with-shrinkwrap/) files provide a safety net for dependencies by allowing you to lock to particular versions even when you explicitly rely on the latest and greatest. Now you can keep the pace of a particular library's releases without a lot of hassle. 
