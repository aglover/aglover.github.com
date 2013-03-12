---
layout: post
title: "Node.js in 3 commands"
date: 2013-03-12 11:55
comments: true
categories: [JavaScript, Node]
---

A few [short years ago](http://www.javaworld.com/community/node/8140) when I started to explore [Node.js](http://nodejs.org/), I remember the installation on my MacBook Pro required downloading the source, compiling and installing it, and then updating paths. _And then_ you had to install [NPM](https://npmjs.org/). 

Since then, [Node](http://www.ibm.com/developerworks/library/j-nodejs/)'s installation has been come tremendously easy and NPM is even _bundled_ with [Node](http://www.ibm.com/developerworks/offers/lp/demos/summary/j-jnodejs.html?ca=drs-). And yet, installing Node (and keeping up w/its rapid release schedule) is even easier with a nifty tool: [Node Version Manager](https://github.com/creationix/nvm). 

With Node Version Manager (or [nvm](https://twitter.com/aglover/status/105824835149627392)) you can be up and running with Node in 3 easy commands.

Step 1: Download and install nvm.

``` bash downloading nvm
curl https://raw.github.com/creationix/nvm/master/install.sh | sh
```

Step 2: Reload your shell.

``` bash reloading .bash_profile
source .bash_profile
```

Step 2.5: Obtain a list of available node versions to install.

``` bash listing available node versions
nvm ls-remote
```

Step 3: Install your desired version of node.

``` bash installing node version 0.10.0
nvm install v0.10.0
```

After that's done, execute a ```node -v``` to verify. Now wasn't that easy? You bet! 