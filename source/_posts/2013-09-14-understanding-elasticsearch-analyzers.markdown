---
layout: post
title: "Understanding ElasticSearch analyzers"
date: 2013-09-14 08:37
comments: true
categories: [elasticsearch, JavaScript, Node, CoffeeScript]
---

{% img right /images/mine/old-beer.jpg %}Sadly, lots of early Internet beer recipes aren't necessarily in an easily digestible format; that is, these recipes are _unstructured_ intermixed lists of directions and ingredients often originally composed in an email or forum post. 

So while it's hard to easily put these recipes into traditional data stores (ostensibly for easier searching), they're perfect for [ElasticSearch](http://thediscoblog.com/blog/2013/05/14/the-democratization-of-search/) in their current form. 

Accordingly, imagine an [ElasticSearch](http://thediscoblog.com/blog/2013/05/17/elasticsearch-on-ec2-in-less-than-60-seconds/) index full of beer recipes, since...well...I enjoy making beer (and drinking it too). 

<!-- more -->

First, I'll add some beer recipes into ElasticSearch using [Node's ElasticSearch Client](https://github.com/phillro/node-elasticsearch-client)(note that the code is [CoffeeScript](http://thediscoblog.com/blog/categories/coffeescript/) though). I'll be adding these beer recipes into a `beer_recipes` index like so: 

``` javascript Adding a beer recipe 
beer_1 = {
 	name: "Todd Enders' Witbier",
 	style: "wit, Belgian ale, wheat beer",
 	ingredients: "4.0 lbs Belgian pils malt, 4.0 lbs raw soft red winter wheat, 0.5 lbs rolled oats, 0.75 oz coriander, freshly ground Zest from two table oranges and two lemons, 1.0 oz 3.1% AA Saaz, 3/4 corn sugar for priming, Hoegaarden strain yeast"
}

client.index('beer_recipes', 'beer', beer_1).on('data', (data) ->
 	console.log(data)
).exec()
```

Note how the interesting part of a recipe JSON document, dubbed `beer_1` is found in the `ingredients` field. This field is basically a big string of valuable text (you can imagine how this string was essentially the body of an email). So while the `ingredients` field is unstructured, it's something clearly that people will want to search on. 

I will add one more recipe for good measure:

``` javascript Adding a second beer recipe
beer_2 = {
 	name: "Wit",
 	style: "wit, Belgian ale, wheat beer",
 	ingredients: "4 lbs DeWulf-Cosyns 'Pils' malt, 3 lbs brewers' flaked wheat (inauthentic; will try raw wheat nest time), 6 oz rolled oats, 1 oz Saaz hops (3.3% AA), 0.75 oz bitter (Curacao) orange peel quarters (dried), 1 oz sweet orange peel (dried), 0.75 oz coriander (cracked), 0.75 oz anise seed, one small pinch cumin, 0.75 cup corn sugar (priming), 10 ml 88% food-grade lactic acid (at bottling), BrewTek 'Belgian Wheat' yeast"
}

client.index('beer_recipes', 'beer', beer_2).on('data', (data) ->
 	console.log(data)
).exec()
```

It's a hot summers day and I'm thinking I'd like to make a beer with _lemon_ as an ingredient (to be clear: I want to use lemon zest, which is obtained from a lemon peel). So naturally, I need to find (i.e. _search for_) a recipe with lemons in it. 

Consequently, I'll search my index for recipes that contain the word "lemon" like so:

``` javascript Searching for lemon
query = { "query" : { "term" : { "ingredients" : "lemon" } } }

client.search('beer_recipes', 'beer', query).on('data', (data) ->
	data = JSON.parse(data)
	for doc in data.hits.hits
		console.log doc._source.style
		console.log doc._source.name
		console.log doc._source.ingredients
).exec()
```

But nothing shows up -- there are no results! Why is that? 

If you look closely in the earlier code example (specifically, the `beer_1` JSON document), you can see that the word "lemons" is in the text (i.e. "...two table oranges and two lemons..."). It turns out, by default, the way values are indexed by ElasticSearch, _lemon_ doesn't necessarily match -- _lemons_ does though.

``` javascript Searching for lemons
query = { "query" : { "term" : { "ingredients" : "lemons" } } }

client.search('beer_recipes', 'beer', query).on('data', (data) ->
	data = JSON.parse(data)
	for doc in data.hits.hits
		console.log doc._source.style
		console.log doc._source.name
		console.log doc._source.ingredients
).exec()
```

Lo and behold, this search returns a hit! But that's inconvenient, to say the least. Basically the words in the `ingredients` field are tokenized _as is_. Hence, a search for "lemons" works while "lemon" doesn't. Note: there are various mechanisms for searching, and a search on "lemon*" should have returned a result.

When a document is added into an [ElasticSearch](http://thediscoblog.com/blog/2013/01/02/scalable-searching-with-elasticsearch/) index, its fields are analyzed and converted into _tokens_. When you execute a search against an index, you search against those tokens. How ElasticSearch tokenizes a document is configurable. 

There are different ElasticSearch analyzers available -- from language analyzers that allow you to support non-English language searches to the [snowball](http://snowball.tartarus.org/) analyzer, which converts a word into its root (or stem and that process of creating a stem from a word is called _[stemming](http://snowball.tartarus.org/algorithms/english/stemmer.html)_), yielding a simpler token. For example, a snowball of "lemons" would be "lemon". Or if the words "knocks" and "knocking" were in a snowball analyzed document, both terms would have "knock" as a stem. 

You can change how documents are tokenized via the index mapping API like so:

``` bash Changing the mapping for an index using cURL
curl -XPUT 'http://localhost:9200/beer_recipes' -d '{ "mappings" : {
  "beer" : {
    "properties" : {
      "ingredients" : { "type" : "string", "analyzer" : "snowball" }
    }
   }
 }
}'
```

Note how the above mapping specifies that the `ingredients` field will be analyzed via the snowball analyzer. Also note, you have to change the mapping of an index _before_ you begin to add documents to it! So, in this case, I'll need to drop the index, run the mapping call above, and then re-add those two recipes.

Now I can begin searching recipes for the ingredient "lemon"  or "lemons".

``` javascript Searching for lemon now works!
query = { "query" : { "term" : { "ingredients" : "lemon" } } }

client.search('beer_recipes', 'beer', query).on('data', (data) ->
	data = JSON.parse(data)
	for doc in data.hits.hits
		console.log doc._source.style
		console.log doc._source.name
		console.log doc._source.ingredients
).exec()
```

Keep in mind that [snowballing can inadvertently](http://stackoverflow.com/questions/3875382/lucene-standard-analyzer-vs-snowball) make your search results _less relevant_. Long words can be stemmed into more common but completely different words. For example, if you snowball a document that contains the word "sextant", the word "sex" will result as a stem. Thus, searches for "sextant" will also return documents that contain the word "sex" (and vice versa). 

ElasticSearch puts a powerful search engine into your clutches; plus, with a little forethought into how a document's contents are analyzed, you'll make searches event more relevant. 

