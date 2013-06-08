---
layout: post
title: "MongoDB to CSV"
date: 2013-06-07 20:01
comments: true
categories: MongoDB
---


Every once in a while, I need to give a non-technical user (like a business analyst) data residing in [MongoDB](http://www.mongodb.org/); consequently, I export the target data as a [CSV file](http://en.wikipedia.org/wiki/Comma-separated_values) (which they can presumably slice and dice once they import it into Excel or some similar tool). Mongo has a handy [export utility](http://docs.mongodb.org/manual/reference/program/mongoexport/) that takes a bevy of options, however, there is an [outstanding bug](https://jira.mongodb.org/browse/SERVER-4224) and some [general confusion](http://stackoverflow.com/questions/6814151/how-to-export-collection-to-csv-in-mongodb) as to how to properly export data in CSV format. 

<!-- more -->

Accordingly, if you need to export some specific data from MongoDB into CSV format, here's how you do it. The key parameters are connection information to include authentication, an output file, and most important, a list of fields to export. What's more, you can provide a query in escaped JSON format.

You can find the `mongoexport` utility in your Mongo installation `bin` directory. I tend to favor verbose parameter names and explicit connection information (i.e. rather than a URL syntax, I prefer to spell out the host, port, db, etc directly).  As I'm targeting specific data, I'm going to specify the collection; what's more, I'm going to further filter the data via a query. 

`ObjectId`'s can be referenced via the `$oid` format; furthermore, you'll need to escape all JSON quotes. For example, if my query is against a `users` collection and filtered by `account_id` (which is an `ObjectId`), the query via the `mongo` shell would be:

``` javascript Mongo Shell Query
db.users.find({account_id:ObjectId('5058ca07b7628c0002099006')})
```

Via the command line &agrave; la `monogexport`, this translates to:

``` bash Collections and queries
 --collection users --query "{\"account_id\": {\"\$oid\": \"5058ca07b7628c0002000006\"}}"
```

Finally, if you want to only export a portion of the fields in a `user` document, for example, `name`, `email`, and `created_at`, you need to provide them via the `fields` parameter like so:

``` bash Fields declaration
--fields name,email,created_at
```

Putting it all together yields the following command:

``` bash Puttin' it all together
mongoexport --host mgo.acme.com --port 10332 --username acmeman --password 12345  \
--collection users --csv --fields name,email,created_at --out all_users.csv --db my_db \
--query "{\"account_id\": {\"\$oid\": \"5058ca07b7628c0999000006\"}}"
```

Of course, you can throw this into a bash script and parameterize the `collection`, `fields`, output file, and query with bash's handy `$1`, `$2`, etc variables. 

Got it?

