---
layout: post
title: "Heroku deployments with Git branches"
date: 2013-12-26 21:46
comments: true
categories: [Heroku, Git]
---

{% img left /images/mine/heroku-logo-light.png %}Single branch development is easy. But this strategy's easiness blows up once you release code to the general public.  If you need to hot-fix an instance of deployed code, while in the midst of a development cycle, single branch development gets in your way by requiring you think. On the other hand, having _more than one_ branch at least allows you to jump back in time via an alternate branch to perform a patch, while not disturbing an unfinished developmental branch. And you can do this without having to think much.

<!-- more -->

Consequently, if you have multiple [Git](http://git-scm.com/) branches (such as those managed via [git-flow](https://github.com/nvie/gitflow))  you can map those branches to different [Heroku](https://www.heroku.com/) environments quite easily. First, you'll want to appropriately name your remote [Heroku](http://thediscoblog.com/blog/categories/heroku/) environments; for instance, you can name the remote repositories `prod`, `test`, `staging`, etc. 

You can add a remote Git repository like so:

``` bash Adding a remote Git repo
git remote add <name> git@heroku.com:<heroku_app_name>.git
```

Where `heroku_app_name` is the name you gave your app or the one automatically created by Heroku via the `heroku create` command. 

With different Heroku environments, you can easily map each one to a Git branch. For instance, a production environment can map to the `master` branch and a test environment can map to a branch called `release`. If you have a development environment, that can map to a branch dubbed `develop` (note, these branch names correlate nicely with git-flow's defaults). 

To deploy the `release` branch to your Heroku test environment, you can issue this command:

``` bash Deploying a topic branch
git push test release:master
```

If you deploy often (i.e. push a lot!) and you can't seem to remember the `release:master` portion, you can create a remote alias like so:

``` bash Adding an alias
git config remote.test.push release:master
```

Thus, deployments to the test environment are as simple as: 

``` bash Simple deployments!
git push test
```

[Heroku's deployment model](http://www.ibm.com/developerworks/library/j-javadev2-21/), which leverages Git, couldn't be any simpler; what's more, setting up a release pipeline with multiple environments mapped to different Git branches is just as easy. Dig it?


