---
layout: post
title: "Mongoid batch inserts"
date: 2013-03-27 11:34
comments: true
categories: [Ruby, MongoDB]
---

In SQL land, all databases support batch inserts. Batch inserts are an effective and efficient mechanism to insert a lot of similar data. That is, instead of issuing x insert statements, you execute 1 insert with x records. This is much more efficient because the insert statement doesn't need to be re-parsed x times, there is only 1 network trip as opposed to x, and in the case of transactions, there is only 1 transaction instead of x. When compared to x inserts, batch inserts are always faster. 

As it turns out, [MongoDB](http://www.mongodb.org/) supports [batch inserts](http://docs.mongodb.org/manual/applications/create/#bulk-insert-multiple-documents)! And just like in SQL land, Mongo's batching feature is much faster at inserting a lot of data in one insert rather than x inserts. 

For example, the [Mongo Ruby driver](https://github.com/mongodb/mongo-ruby-driver)'s [insert method takes a collection](https://github.com/mongodb/mongo-ruby-driver/blob/master/lib/mongo/collection.rb#L371); thus, you can insert an array of hashes quite efficiently. Even if you are using a [ODM like Mongoid](http://mongoid.org/en/mongoid/index.html), you can still perform batch inserts as all you need to do is get a reference to the model object's underlying collection and then issue an ```insert``` with an array of hashes matching the collection's intended document structure.

For instance, to insert a collection of ```Tag``` models (each having 3 fields: ```name```, ```system_tag```, and ```account_id```) in one fell swoop I can do the following:

``` ruby Batch inserts with Mongoid model example
tags = ['a', 'bunch', 'of', 'tags'].collect { |tag| {name: tag, system_tag: true, account_id: id} }
Tag.collection.insert tags
```

In the code above, the ```insert``` takes a collection of hashes; what's more, the ```insert``` is tied to the ```tags``` collection via the ```Tag.collection``` call. 

Batch inserts are always faster if you have a lot of similar documents -- in our case, we saw a _tremendous_ performance increase when employing batching. 