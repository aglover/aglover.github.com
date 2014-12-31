---
layout: post
title: "Continuous Delivery for Heroku with Jenkins"
date: 2014-01-24 20:43
comments: true
categories: [Git, Heroku, DevOps, cloud, Continuous Integration]
---

{% img left /images/mine/jenkins.png %}A [continuous delivery pipeline](http://thediscoblog.com/blog/categories/devops/) that leverages [Jenkins](http://jenkins-ci.org/) and targets [Heroku](http://heroku.com/) is fairly simple to set up, provided you install the Jenkins Git plugin. With this pipeline, changes to a specific Git branch will result in a Heroku deployment. 

For this deployment process to work nicely, you should use at least two Git branches, as you'll want to have one branch targeted for auto-deploys and another that doesn't (as it represents active development).  For example, following the [git-flow](http://nvie.com/posts/a-successful-git-branching-model/) convention, those two branches could be named `development` and `master`, where changes to `master` are deployed to Heroku and changes to `development` aren't. Thus, you will have at least two Jenkins jobs that monitor _each_ of these branches. 

<!-- more -->

Naturally, this pipeline process is language agnostic -- [Node](http://thediscoblog.com/blog/categories/node/), [Ruby](http://thediscoblog.com/blog/categories/ruby/), [Java](http://thediscoblog.com/blog/categories/java/) -- it doesn't matter what you do during your build as this entire process is choreographed via Git. 

When approaching Heroku auto-deployment from Jenkins, _don't bother with Heroku's API_ because it's much easier to use the Git publisher feature of Jenkins to push a branch from your repository to Heroku (which uses Git anyway). 

At a high level, you'll need to define a Jenkins job that monitors your `master` Git branch; if there are changes, this job will run whatever your build needs to do and as a post-build step you can publish that branch to Heroku. It's that easy. 

To configure this pipeline, you will need the [Git plugin](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin). With the Git plugin installed, create a job and in the Source Code management section, add your source Git repository and then add another repository which is the Heroku remote repository. 

{% img center /images/mine/scm-jenkins1.png %}

Be sure to give the Heroku repository a name like `heroku`. This is done by clicking the Advanced button under the Credentials section.

Second, in the Post-build Actions section, you'll configure a Git Publisher. 

{% img center /images/mine/git-pub.png %}

In this case, the Git repository you are going to publish to will be the Heroku one defined earlier.  Hit the Add Branch button and be sure to indicate the `master` branch as the Branch to push and the Target remote name should be the name your gave to the remote Heroku repository in the Source Code Management section (i.e. `heroku`). 

{% img center /images/mine/scm-jenkins2.png %}

Depending on how you've set up your Build Trigger on your job, when a build completes, Jenkins will push the resultant snapshot to the Heroku repository, [resulting in a deployment](http://stackoverflow.com/questions/16840196/tutorial-on-pushing-to-heroku-via-jenkins/20828183#20828183)! Now wasn't that easy, man?

