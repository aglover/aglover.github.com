---
layout: post
title: "MongoDB primary keys are your friend"
date: 2013-06-22 15:00
comments: true
categories: MongoDB
---

All documents in a [MongoDB](http://www.mongodb.org/) collection have a primary key dubbed `_id`. This field is automatically assigned to a document upon insert, so there's rarely a need to provide it. What's interesting about the `_id` field is that it is _time based_. That is, the underlying type of `_id`, which is `ObjectId`, is a [12-byte BSON type](http://docs.mongodb.org/manual/reference/object-id/), and 4 of those bytes represent the seconds since Unix epoch. 

<!-- more --> 

What's also special about the `_id` field is that it is automatically indexed as you can see below by calling `getIndexes` on any collection.

``` javascript All MongoDB collections have an _id field as an index
> db.things.getIndexes()
[
     {
          "v" : 1,
          "key" : {
               "_id" : 1
          },
          "ns" : "test.things",
          "name" : "_id_"
     }
]
```

And as everyone remembers from traditional RDBMSs, [indexes are important](http://en.wikipedia.org/wiki/Database_index) because they can make document retrieval faster; nevertheless, indexes do consume memory and there is a slight performance penalty when inserting documents as all corresponding indexes must be updated. Thus, while you should seriously consider using indexes, you need to be economical in their usage. 

Naturally, searching by a document's `_id` is only convenient when you _know_ it. More often than not, documents are searched via other fields and if you find yourself searching [via a time series](http://cookbook.mongodb.org/patterns/date_range/), such as `created_at` then you are in for a treat. 

Imagine a collection dubbed `logs` that contains simple documents capturing various log messages. A sample document could look like so:

``` javascript A simple document in a logs collection
{
     "_id" : ObjectId("51c4ab6d4d6906d494460728"),
     "message" : "crashed, no such method exception",
     "type" : "crash",
     "created_at" : ISODate("2013-06-21T19:37:17.992Z")
}
```

What if I wanted to find all log messages for some date, like today? I could write my query like so:

``` javascript finding all logs created since June 20th, 2013
db.logs.find({created_at:{'$gt': new Date(2013, 5, 20)}})
```

If I throw an explain to that query, I can see that because I do not have an index on `created_at`, a basic cursor is leveraged and all documents in the collection were scanned in order to retrieve my result. 

``` javascript An explain plan attached to my find
> db.logs.find({created_at:{'$gt': new Date(2013, 5, 20)}}).explain()
{
     "cursor" : "BasicCursor",
     "isMultiKey" : false,
     "n" : 2,
     "nscannedObjects" : 4,
     "nscanned" : 4,
     "nscannedObjectsAllPlans" : 4,
     "nscannedAllPlans" : 4,
     "scanAndOrder" : false,
     "indexOnly" : false,
     "nYields" : 0,
     "nChunkSkips" : 0,
     "millis" : 0,
     "indexBounds" : {

     },
     "server" : "ghome-computer.home:27017"
}
```

As you can see, searching via the `created_at` field can be inefficient; thus, you might be tempted to throw an index on that field. This would naturally make that particular query more efficient, however, you would incur the cost of a new index which is more memory consumed and inserts would be slightly slower due to an update to that newly created index.

As it turns out, because the `_id` field embeds Unix epoch in it, you can just as easily craft a find expression _without_ including the `created_at` field. For example, the [MongoDB Ruby driver](https://github.com/mongodb/mongo-ruby-driver) allows you to create `ObjectId`'s from a `Time` like so: 

``` ruby Creating a new ObjectId via the from_time factory method
yesterday = Time.now - (60*60*(24*1)) 
custom_id = BSON::ObjectId.from_time(yesterday) 
=> BSON::ObjectId('51c397800000000000000000') 
```

As you can see, I've created a new `ObjectId` via the `from_time` factory method.  51c397800000000000000000 is a hexadecimal representation and the first 8 digits represent the time with everything else zeroed out. Note, you can create `ObjectId` types via some notion of time in other language drivers as well. 

Now I can leverage my `custom_id` in any `find` expression. Via the Ruby driver, I can also attach an `explain`, which'll demonstrate the usage of the free `_id` index. 

``` ruby Using a date derived ObjectId forces a find to use the _id index
 mongodb[:logs].find({_id: {'$gt' => custom_id}}).explain

=> {"cursor"=>"BtreeCursor _id_", "isMultiKey"=>false, "n"=>1, "nscannedObjects"=>1, "nscanned"=>1, ....} 
```

If you see `BtreeCusor`, then you know you're using an index; if you see `BasicCursor`, you know you're not. 

Thus, if you find yourself executing queries and creating indexes for some time or date field like `created_at`, you might be better off just using Mongo's `_id` field as it already embeds the notion of _created at_ and is indexed by default. Dig it?
