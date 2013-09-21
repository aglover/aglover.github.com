---
layout: post
title: "I like my ElasticSearch a la Node.js"
date: 2013-09-21 08:57
comments: true
categories: [elasticsearch, Node, CoffeeScript]
---

{% img right /images/mine/320px-Pie_A_La_Mode.jpg %}While [ElasticSearch](http://thediscoblog.com/blog/categories/elasticsearch/) is easy enough to work with via its RESTful HTTP API, there are [myriad client libraries](http://www.elasticsearch.org/guide/clients/) available in almost every conceivable programming language. If [Node.js](http://thediscoblog.com/blog/categories/node/) is your language of choice, then there's at least two actively supported libraries available.
 
My favorite is dubbed, albeit rather dully, "[Elastic Search Client](https://github.com/phillro/node-elasticsearch-client)", but don't let the library's unimaginative name fool you: this is a handy library that allows you to do everything you could do via cURL with the added benefit of JavaScript callbacks. Best of all, you can use the Node Elastic Search Client in [Coffeescript](http://thediscoblog.com/blog/categories/coffeescript/), which is a handy language that makes JavaScript less verbose and that ultimately compiles into JavaScript. 

<!-- more -->

Accordingly, if you're familiar with the typical RESTful API calls for creating and mapping indexes, plus indexing and searching documents, then you'll find Elastic Search Client easy enough to pick up. 

To get started, add the library as a dependency in your NPM `package.json` file like so:

``` json My package.json file that lists the latest version of elasticsearchclient.
"dependencies":{
	"elasticsearchclient" : "latest",
	"mocha" : "latest",
	"should" : "latest",
	"coffee-script" : "latest"
}
```

Since I happen to prefer [CoffeeScript](http://coffeescript.org/) over JavaScript, I've also included CoffeeScript as a dependency. 

Like any Node library, you'll need to include a library it via a `require` statement to make use of it. In this case, I'll require `'elasticsearchclient'` and then connect to a local instance like so:

``` javascript Initalizing a new client
ElasticSearchClient = require 'elasticsearchclient'
client = new ElasticSearchClient { host: 'localhost', port: 9200 }
```

Going forward, I'm going to reference a few variables, namely `indexName`, which is "beer_recipes" and `objName`, which is "beer". What's more, this code is using [Mocha](http://visionmedia.github.io/mocha/) and [should](https://github.com/visionmedia/should.js/), so that'll explain the various specification related statements in the examples below.

With a connection to an Elasticsearch server, I can consequently create an index like so:

``` javascript Creating an index in a before clause
describe 'Create and update an index', ->
	before (done) ->
		client.createIndex(indexName).on 'data', (data) ->
			data = JSON.parse data
			data.should.be.ok
			done()
		.exec()
```

And I can update the index's mapping too. Just use the `putMapping` call:

``` javascript Updating an Index mapping
it 'should support a mapping put which changes the analyzer', (done) ->
	snowball = { "mappings" : { "beer" : { "properties" : { "ingredients" : { "type" : "string", "analyzer" : "snowball" } } } }}
	client.putMapping(indexName, objName, snowball).on 'data', (data) ->
		data = JSON.parse data
		data.should.be.ok
		done()
	.exec()
```

In this case, the actual mapping JSON document is identical to what I'd have to pass via cURL, for instance. 

You should start to notice a pattern with respect to how the Elastic Search Client deals with callbacks -- using an `on` method, you can register a callback for `'data'`, which essentially entails the response from the server; moreover, you can also register a listener for `'error'`, which as you can imagine, gets invoked if there is a problem. 

Deleting an index is just as easy as creating one too: 

``` javascript Deleting an index
after (done) ->
	client.deleteIndex(indexName).on 'data', (data) ->
		data = JSON.parse data
		data.should.be.ok
		done()
	.exec()
```

To index a document, you simply use the `index` function like so:

``` javascript Indexing a document
beer = { "name": "Todd Enders' Witbier", "style": "wit, Belgian ale, wheat beer", "ingredients": "4.0 ..."}
client.index(indexName, objName, beer).on 'data', (data) ->
	data = JSON.parse data
	data.ok.should.equal true
.exec()
```				

Note in this case, I pass along an index name, and object name, and a JSON document to index. Because JSON marries so nicely with JavaScript, this library is quite nice, don't you think?

Searching is just as easy (as I'm sure you already guessed). You create a query JSON document and pass it along to the `search` method like so:

``` javascript Searching is just as easy!
iquery = { "query" : { "term" : { "ingredients" : "lemons" } } }
client.search(indexName, objName, iquery).on 'data', (idata) ->
	result = JSON.parse idata
	result.hits.total.should.equal 1
	done()
.exec()
```

The resultant response from the server is a JSON document that must be parsed and dealt with accordingly. 

Of course, you can grab an individual document via its id just as easily:

``` javascript Getting an individual document via its document id
client.get(indexName, objName, doc_id).on 'data', (getData) ->
	doc = JSON.parse getData
	doc._source.name.should.equal "Todd Enders' Witbier"
	done()
.exec()
```

Node's Elastic Search Client is a breeze to pick up; plus, the natural union of JSON and JavaScript make Node a perfect language for interfacing with ElasticSearch. Dig it?
