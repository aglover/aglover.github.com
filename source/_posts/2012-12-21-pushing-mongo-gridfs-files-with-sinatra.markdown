---
layout: post
title: "Pushing Mongo GridFS files with Sinatra"
date: 2012-12-21 21:19
comments: true
categories: [Ruby, MongoDB]
---

[We](http://www.app47.com/) recently implemented a new feature that required two interesting aspects: storing a file in [MongoDB](http://www.mongodb.org/) using [GridFS](http://docs.mongodb.org/manual/applications/gridfs/) (think traditional [Blob](http://en.wikipedia.org/wiki/Binary_large_object)) and then pushing that file down to a browser. Along the way, I discovered a lot of questions on [stackoverflow](http://stackoverflow.com/) and the like pertaining to various aspects of GridFS and [Sinatra](http://www.sinatrarb.com/) pushes, so I thought I'd explain what we did. 

First off, GridFS is a specification for storing and retrieving large documents. In essence, Mongo breaks a large document into smaller documents (called _chunks_) and stores them in one collection and associates the meta data related to the aggregated chunks into another collection. It's a fairly handy feature and in our case, it made sense to use [GridFS](http://docs.mongodb.org/manual/faq/developers/#faq-developers-when-to-use-gridfs) rather than [S3](http://aws.amazon.com/s3/) (or the underlying file system).  

All MongoDB drivers implement a similar access pattern to leverage GridFS and they hide the complexity of working with chunks. For example, [the Ruby driver](https://github.com/mongodb/mongo-ruby-driver) simply provides a [single interface to manage the two GridFS collections](https://github.com/mongodb/mongo-ruby-driver/wiki/GridFS). In fact, [the interface](https://github.com/mongodb/mongo-ruby-driver/blob/master/lib/mongo/gridfs/grid.rb) basically provides two methods: `get` & `put` which might seem somewhat limiting, especially because `get` takes an `_id`! What's more, you can add rich meta data to a GridFS document (via the `put` call) -- data you'd conceivably want to query by, but at first glance, you can't via the `get` method. Nevertheless, because GridFS is ultimately two collections in Mongo, you can query them as you would any other collection. 

Thus, finding files by some attribute other than their `_id` is as easy as writing the corresponding query: 

``` ruby GridFS example
file = @mongo['user_apks.files'].find({:filename => user_id.to_s}).first
```

Keep in mind, however, that the result returned above is a JSON document -- i.e. the variable `file` above isn't a pointer to an actual I/O instance but simply a JSON document with its details. To get the actual file, use the `get` method provided by the driver's GridFS facade -- this'll handle the details of grabbing the file from GridFS. The `get` method takes an `_id`, which you can get from the `file` document. Thus, to get an instance of a readable file, you can grab it like so:

``` ruby get via _id
@grid.get(file['_id'])
``` 

Thus, with the `get` call you can actually get ahold of a file instance and not some JSON document describing it. 

To push this instance down to a browser using [Sinatra](http://thediscoblog.com/blog/2012/12/10/sinatra-coffeescript-and-haml-swinging-in-4-steps/), you need to do 3 things:

- set the content type
- set the attachment name
- write the file to the response

In our case, the file is an [Android](http://developer.android.com/index.html) app; accordingly, the content type is `application/vnd.android.package-archive` and as you'll note, the attachment is simply the name of the `.apk` file. Finally, the response to the request is written to by _reading_ from the corresponding GridFS file:

``` ruby Sinatra push
get '/some/url/:account_id/:user_id', :agent => /Android/ do |account_id, user_id|
  file = # get the file from GridFS
  content_type 'application/vnd.android.package-archive'
  attachment "#{file.filename}.apk"
  response.write file.read
end
``` 
As you can see, it's all quite easy -- GridFS is simply a facade for two collections; moreover, forcing a download in Sinatra is three straightforward steps. Can you dig it, man?