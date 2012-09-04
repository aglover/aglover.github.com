---
layout: post
title: "MongoDB from the trenches: masochistic embedded collections"
date: 2012-09-03 21:47
comments: true
categories: [MongoDB]
---

[MongoDB](http://www.mongodb.org/) supports rich documents that can include, among other things, embedded documents. This feature embodies a _has-a_ relationship quite nicely and can, if modeled properly, reduce the number of finds required to ascertain certain data as there are no joins in Mongo. 

As classic example of embedding a collection of documents inside a parent document is contact addresses (i.e. mailing, email, twitter, etc) associated with a person. Think business cards. You can, of course, model this in any number of ways -- in the traditional relational world, this would be a one-to-many relationship between at least two tables. Nevertheless, with a document-oriented database, you can model a parent `person` document with an embedded collection of `contacts` that are each themselves documents containing, say, _type_ (i.e. phone, twitter, email) and _value_ (which could be 555-555-555, @jon_doe, etc). 

This relationship with Mongo works nicely _if the child embedded document never needs to exist outside of its parent_. In the case of a business card, the contact document representing a phone number, for example, doesn't necessarily make sense outside the context of the person who it belongs to. With this relationship, you can easily find a particular person via his/her phone number (that is, via Mongo's query language, you can reach inside arrays via its [dot notion](http://www.mongodb.org/display/DOCS/Dot+Notation+(Reaching+into+Objects)) effortlessly). And, once you have a handle to a person, you don't need to execute a series of finds to ascertain contact information -- it's all right there.  

Nevertheless, things start to get painful quickly if you'd like to operate solely on a singular embedded document. That is, if you execute finds that are intended to deal with the expected resultant embedded document, you're in for some work: as of Mongo 2.2, you can't select a singular document from within a collection residing in a parent via a query. A find in this case will pull _everything_ -- it's up to you (i.e your application) to filter things. 

An example will probably help: imagine the business card example from earlier -- a `person` document containing an embedded collection of `contacts`:

``` javascript person document
{
	first_name: 'Andrew',
	last_name: 'Glover',
	contacts: [ 
	{
	  type:	'cell',
	  value: '555-555-5555',
	  last_updated: 2012-09-01 23:41:51 UTC
	},
	{
	  type:	'home',
	  value: '555-555-5551',
	  last_updated: 2012-02-11 12:21:11 UTC
	}
	]
}
```

To find this document by a phone number is easy: 

``` javascript finding a person by phone number
 db.persons.find({'contacts.value':'555-555-5555'})
```

But what if you wanted to find the `contact` that was recently updated, say since the beginning of the month, and change its value or add some additional meta-data? The query _you'd like_ would look something like:

``` javascript finding via last_updated
db.persons.find({'contacts.last_updated': {$gte: datetime(2012, 8, 1)}})
```

This query works and will match the person 'Andrew Glover' -- but the catch here is that what is returned is the entire document. You can add query limiters if you'd like (i.e. `{contacts:1}`), however, that will merely return a `person` document with only a collection of `contacts`. Thus, you are left to iterate over the resultant collection of `contacts` and work your magic that way. That is, you still have to find the contact document that was edited this month! In your code! 

No big deal, you say? This particular example is, indeed, a bit contrived; however, imagine if the overall document is quite large (maybe it's not a `person` but an `organization`!) and that the embedded collection is also lengthly (how many employees does Google have?). Now this simple update is pulling a lot of bytes across the wire (and taxing Mongo in the process) and then your app is working with a lot of bytes in memory (now the document is taxing your app!). Did you want this operation to happen quickly, under load too? 

Thus, with embedded document collections, if you envision having to work with a particular embedded document _in isolation_, it is better, at this point, to model has-a relationships with distinct collections (i.e. in this example, life would be much easier if there is a `person` collection and a `contacts` one). Indeed, the flexibility of document-oriented, schema-less data stores is a boon to rapid evolutionary development. But you still have to do some thinking up front. Unless, of course, you're a masochist. 


I'm a huge fan of Mongo. Check out some of the articles, videos, and podcasts that I've done, which focus on Mongo, including:

 * [Java development 2.0: MongoDB: A NoSQL datastore with (all the right) RDBMS moves](http://www.ibm.com/developerworks/java/library/j-javadev2-12/)
 * [Video demo: An introduction to MongoDB](http://public.dhe.ibm.com/software/dw/demos/jmongodb/index.html)
 * [Eliot Horowitz on MongoDB](http://www.ibm.com/developerworks/java/library/j-gloverpodcast/#Horowitz)
 * [10gen's Steve Francia talks MongoDB](http://www.ibm.com/developerworks/java/library/j-gloverpodcast4/index.html#francia)