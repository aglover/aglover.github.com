---
layout: post
title: "Custom git commands in 3 steps"
date: 2014-03-29 18:12
comments: true
categories: [Linux, OSX, Git]
---

I'm lazy and so I seek ways to [reduce repetitious activities](http://threevirtues.com/). For instance, I've spent a lot of time in a terminal typing Git commands. A few of the more common commands, I've [aliased](http://tldp.org/LDP/abs/html/aliases.html). If I want to see a list of branches, I used to type: 

``` bash Listing Git branches
$> git branch -v -a
```

But after adding an alias to my bash profile, I simply type `gb`. I've done this for a few commands like `git commit`, which is `gc` and `gca` for the `-a` flag. 

<!-- more -->

Occasionally, aliases aren't enough and when it comes to Git, you can create custom commands that can be referenced like so:

``` bash Your custom Git command
$> git my-command 
```

To create a custom command, you first need to create a file named `git-my-command`; second, you must place the resultant file on your path. Finally, you need to make the file executable. You can write this file in [Bash](http://thediscoblog.com/blog/categories/linux/), [Ruby](http://thediscoblog.com/blog/categories/ruby/), or Python -- it doesn't matter. 

For example, I tend to find myself [stashing](http://git-scm.com/book/en/Git-Tools-Stashing) some uncommitted changes and then later popping those stashed changes onto a new branch. I end up executing the following steps:

``` bash A simple Git flow
$> git stash
$> git stash branch some_branch
```

The key step I want to simplify is the last one -- I'm lazy and I'd rather not type 4 words. I'd rather type `git unstash some_branch` because it saves me one word. 

Following the three simple steps I mentioned above, I'll first create a file in my `~/bin` directory called `git-unstash`. The `~/bin` directory is in my path because my `.bashrc` has this line: `PATH=$PATH:$HOME/bin`. 

My `git-unstash` script will be simple -- it takes an argument (the branch name, i.e. `$1`); therefore, the script does a simple check to ensure the branch name is provided. 

``` bash Custom Git command: unstash
#!/bin/bash

((!$#)) && echo No branch name, command ignored! && exit 1

git stash branch $1
```

After I'm done writing it, I'll do a quick `chomd +x` and all three steps are accomplished. 

Now my new flow is this:

``` bash A simple Git flow
$> git stash
$> git unstash some_branch
```

Custom Git commands are that simple to invent -- first, create a file named `git-my-command`. Next, place it on your path; and, finally, make it executable. Be lazy and carry on, baby! 