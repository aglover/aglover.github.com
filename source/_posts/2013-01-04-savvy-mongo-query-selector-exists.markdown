---
layout: post
title: "Savvy Mongo query selector: exists"
date: 2013-01-04 11:57
comments: true
categories: [MongoDB]
---

[MongoDB](http://www.mongodb.org/) supports a [rich query language](http://docs.mongodb.org/manual/applications/read/); in fact, its support of dynamic queries is one of its [more distinguishing features](http://public.dhe.ibm.com/software/dw/demos/jmongodbdocs/index.html) compared to other datastores in the [NoSQL](http://www.infoworld.com/d/data-management/flexing-nosql-mongodb-in-review-185922) world. Mongo's query language has a variety of query selectors that enable you to [fashion some powerful document searches](http://www.ibm.com/developerworks/training/kp/j-kp-mongo/). One particular [query selector that comes in handy](http://docs.mongodb.org/manual/reference/operators/#element) from time to time is `$exists`. 

Because [Mongo is document oriented](http://www.ibm.com/developerworks/java/library/j-javadev2-12/) and thus lacks a rigid schema, documents (for better or for worse) can have varying structures within a collection. In practice, you probably don't see vastly differing documents within a collection; however, from time to time, various document fields might differ (as in, _they might not be present_). Take for example, the classic example of a business card: some cards (i.e. documents) might list a fax number while others might omit it. As another example, as an application and its corresponding data evolves, new fields might be added (or removed). 

The `$exists` query selector facilitates finding documents that have (or do not have) specific fields. On more than one occasion, I've employed this query selector to find documents in need of a surgical update. For example, in a collection dubbed `words` with word documents that each contain an embedded `definitions` document collection, I can find those particular definitions that do not have a `part_of_speech` element like so:

``` javascript $exists query selector in action
db.words.find({"definitions.part_of_speech":{"$exists":false}}).sort({"spelling":1}).limit(100)
```

Note that the `$exists` query selector takes a boolean -- `true` or `false`. 

As you can probably ascertain, Mongo's `$exists` is slightly [different than SQL's](http://www.techonthenet.com/sql/exists.php) `exists` -- in fact, in SQL there is no way to fashion a query to find a row not containing a column! Can you dig it? 
