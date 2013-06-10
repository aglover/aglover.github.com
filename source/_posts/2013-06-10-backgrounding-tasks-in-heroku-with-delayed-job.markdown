---
layout: post
title: "Backgrounding tasks in Heroku with Delayed Job"
date: 2013-06-10 12:56
comments: true
categories: [Ruby, Rails, MongoDB, Heroku]
---


{% img right /images/mine/heroku-logo-light.png %}Long running web requests are bad. They create resource contention among other waiting incoming requests; what's more, with HTTP servers like [Unicorn](https://github.com/defunkt/unicorn) (which is rapidly becoming the de facto HTTP server for Rails apps running on [Heroku](https://get.heroku.com/)), long running requests are summarily terminated after some threshold (for example, 30 seconds) leaving users with a nasty timeout error (and, of course, other users complaining that it takes forever for your site to do anything). Luckily, with frameworks like [Delayed Job](https://github.com/collectiveidea/delayed_job), backgrounding long running tasks [couldn't be any easier](https://devcenter.heroku.com/articles/delayed-job). 

<!-- more -->

To leverage Delayed Job in Heroku, you'll need for follow a few steps. In my case, since I'm using [Mongoid](http://mongoid.org/en/mongoid/index.html), I'm going to use an [alternate Active Record Delayed Job tie-in](https://github.com/collectiveidea/delayed_job_mongoid) dubbed `delayed_job_mongoid`. The reason for this [Active Record](http://guides.rubyonrails.org/) integration will become obvious once I show you how to actually force a long running task to be asynchronous. Regardless if you are using Mongoid or not, the Active Record integration is identical. 

First and foremost, you'll need to add `delayed_job_mongoid` to your `Gemfile` (or if you aren't using Mongoid, add `delayed_job_active_record`).  Delayed Job stores jobs in your database; consequently, after you run `bundle install`, proceed to update your underlying MongoDB instance with a new index by running:

``` bash Adding an index and new collection to MongoDB
$> script/rails runner 'Delayed::Backend::Mongoid::Job.create_indexes'
```

Obviously, if you're using a normal RDMBS, your command will be slightly different (i.e. `rake db:migrate`).

After that step is complete, you'll need to generate a `delayed_job` script via:

``` bash Generating the Delayed Job script
$> rails generate delayed_job
```

This script allows to you execute the Delayed Job framework with a number of options. As it turns out, you don't really need this script as Delayed Job hooks into Rake as well; it is via the Rake integration that Heroku will work with Delayed Job. 

Now that you've set up 80% of the infrastructure required to support Delayed Job, you next need to background some long running task. This is the fun part and like everything else so far, it couldn't be any easier. 

Delayed Job hooks directly into Active Record and thus, you can background some unit of work on a model object by inserting a `delay` call. For example, in my case, I had a `Group` model object that when updated under certain circumstances required that _all associated_ users also be updated (wouldn't it be nice if [MongoDB supported triggers](https://jira.mongodb.org/browse/SERVER-124)?). As you can imagine, updating hundreds of users is rather time intensive. 

Thus, the user update logic was thrown into a special method (dubbed `aysnc_update_users`) and was then invoked like so: 

``` ruby Adding in a delay backgrounds the async_update_users call
group.delay.async_update_users 
```

This call essentially serializes the logic here into a collection called `delayed_backend_mongoid_jobs` which can then be invoked asynchronously by another process. And therein lies your last few steps. 

Heroku has the notion of [worker dynos](https://devcenter.heroku.com/articles/background-jobs-queueing), which are background processors -- think of worker dynos as engines capable of doing what ever you want them to do. In the case of Delayed Job, you want those worker dynos to invoke jobs residing in the `delayed_backend_mongoid_jobs` collection (which is conceptually a queue). 

To configure a worker dyno, you'll need to do 2 things. First, provision one like so:

``` bash Using Heroku CLI to fire up a worker dyno
$> heroku ps:scale worker=1
```

You'll then need to update your `Procfile` to include the command the worker dyno should invoke:

``` ruby Updated Procfile command
worker:  bundle exec rake jobs:work
```

As you can see, the worker dyno in this case is invoking a [Rake task](http://rake.rubyforge.org/) (`jobs:work`) which is asynchronously polling for new jobs in your conceptual queue and invoking them as they are found. 

Finally, deploy to Heroku and keep an eye on the queue for jobs -- you can isolate the dyno workers logs via 

```
$> heroku logs -p worker -t
```

Was that easy or what? 

