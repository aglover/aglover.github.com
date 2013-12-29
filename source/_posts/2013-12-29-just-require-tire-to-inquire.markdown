---
layout: post
title: "Just require tire to inquire"
date: 2013-12-29 12:53
comments: true
categories: [Ruby, elasticsearch]
---

{% img right /images/mine/es-bonzai.jpg %}In the land of [Ruby](http://thediscoblog.com/blog/categories/ruby/) there's a few client libraries for [Elasticsearch](http://thediscoblog.com/blog/categories/elasticsearch/), however, one stands above the rest with a comprehensive set of features and remarkable [documentation](http://karmi.github.io/retire/): [Tire](https://github.com/karmi/retire). 

With its DSL based API, you'll find working with [Elasticsearch](http://www.ibm.com/developerworks/library/j-javadev2-24/) straightforward and a lot of fun. And even though the project's repository has been renamed to [retire](https://github.com/karmi/retire/wiki/Tire-Retire), the Tire library 

{% blockquote Karel Minarik https://github.com/karmi/retire/wiki/Tire-Retire Tire Retire %}
will continue to work...bugs will be fixed and important features will be added.
{% endblockquote %}

In my opinion, for the time being, Tire is still superior to the [elasticsearch-ruby](https://github.com/elasticsearch/elasticsearch-ruby) alternative in terms of features and its elegant DSL. 

<!-- more --> 

To begin using Tire, grab the gem. For instance, if you're using Bundler, then add the following line to your `Gemfile`:

``` ruby Adding Tire to a Gemfile
gem 'tire' 
```

After running a `bundle install`, you'll be able to require Tire.

``` ruby Requiring Tire
require 'tire' 
```

Tire assumes a locally running instance of Elasticsearch; if you wish to connect to a remote node, then create a `configure` block and set the `url` to a remote Elasticsearch instance. 

``` ruby Configuring a remote Elasticsearch instance
Tire.configure do
  url environment['elasticsearch_host']
end
```

In the code above, the `url` is set the value of `elasticsearch_host`. 

##### Dealing with Indexes

First and foremost to working with Elasticsearch is the creation of an index, which can be thought of as a database. Search-able documents are then stored in an index and the process of adding documents to an index is known as _indexing_. 

Consequently, to create an index, you simply name it an issue a `create` in an `index` block. 

``` ruby Creating an index
Tire.index 'beer_recipes' do
  create
end
```

In this case, the index is named 'beer_recipes'. 

As I've [covered before](http://thediscoblog.com/blog/2013/09/14/understanding-elasticsearch-analyzers/), you can alter how Elasticsearch indexes a document by providing a customized index mapping. In the code below, I've specified that the `ingredients` property of the `beer` type will be analyzed using the snowball algorithm, which converts a word into its root, yielding a simpler token (i.e. 'lemons' becomes 'lemon', 'jazzy' becomes 'jazz'). 

``` ruby Changing the mapping of an index
Tire.index 'beer_recipes' do
  create mappings:  {
  	beer: {
  		properties: { 
  			ingredients:  { type: 'string', analyzer: 'snowball' } 
  		} 
  	}
  }
end
```

Finally, to delete an index, simply issue a `delete`, like so:

``` ruby Delete an index
Tire.index 'beer_recipes' do
	delete
end
```

Of course, the whole point of [Elasticsearch is search](http://thediscoblog.com/blog/2013/05/14/the-democratization-of-search/)! And searching with Tire is super simple; what's more, Tire offers a slick feature of allowing you to define model objects for both indexing and searching (think [ActiveRecord](https://gist.github.com/karmi/3200212) integration). 

##### Indexing & Searching with Tire

In [Elasticsearch](http://thediscoblog.com/blog/categories/elasticsearch/), an index can be thought of as a database and a _type_ can be thought of as a database table. Accordingly, when you index a document, you give it a type and associate properties, which essentially act like columns. 

Indexing a document is executed via the `store` command. In the code below, a document is stored as a `beer` type with three properties: `name`, `style`, and `ingredients`. The `refresh` command updates the index and accordingly makes this newly indexed document available for search. 

``` ruby Indexing a document
Tire.index 'beer_recipes' do
	store type: 'beer',
    name: "Todd Enders' Witbier",
    style: "wit, Belgian ale, wheat beer",
    ingredients: "4.0 lbs Belgian pils malt, 4.0 lbs raw soft red winter wheat, 0.5 lbs rolled oats, 0.75 oz coriander, freshly ground Zest from two table oranges and two lemons, 1.0 oz 3.1% AA Saaz, 3/4 corn sugar for priming, Hoegaarden strain yeast"
  refresh
end
```

Consequently, to search for a document in an index, you use the `search` method, which returns a list of results. 

``` ruby Searching for a document
search_res = Tire.search('beer_recipes') do
	query do
		term :ingredients, 'lemon'
	end
end

assert_equal 1, search_res.results.size
assert_equal "Todd Enders' Witbier", search_res.results[0].name
```

In the code above, the `search` block defines a simple query where the `ingredients` property is searched for the term 'lemon', which yields one result. Note, the resultant list contains maps whose keys are the document's properties (i.e. `name`, `style`, and `ingredients`). 

You can use normal Ruby objects for both indexing and searching in Tire. The only requirement is that your model object provide a `type`, `_type` or `document_type` method as well as a `to_indexed_json` method. 

In this case, I've defined a `Beer` class that includes the two required methods as well as a class method that enables searching the `ingredients` property.

``` ruby Using a model object
class Beer

	attr_reader :name, :style, :ingredients

	class << self
		def search_ingredients_for(ingredient)
			search_res = Tire.search('beer_recipes') do
	    	query do
	    		term :ingredients, ingredient
	  		end
	  	end
	  	search_res.results
		end
	end

	def initialize(attributes={})
		@attributes = attributes
    @attributes.each_pair { |name,value| instance_variable_set :"@#{name}", value }
  end

  def type
	  'beer'
  end

  def to_indexed_json
    @attributes.to_json
  end

end
```

Next, to make use of my `Beer` model object, I need to configure Tire to use it via the `wrapper` command. Once that is done, I can use my `Beer` object to index beers and to search for them.

``` ruby 
Tire.configure do
	wrapper Beer #forces results to be of type beer!
end

Tire.index 'beer_recipes' do

	store Beer.new type: 'beer',
    name: "Todd Enders' Witbier",
    style: "wit, Belgian ale, wheat beer",
    ingredients: "4.0 lbs Belgian pils malt, 4.0 lbs raw soft red winter wheat, 0.5 lbs rolled oats, 0.75 oz coriander, freshly ground Zest from two table oranges and two lemons, 1.0 oz 3.1% AA Saaz, 3/4 corn sugar for priming, Hoegaarden strain yeast"

  refresh
end

results = Beer.search_ingredients_for 'lemons'
assert_equal 1, results.size
assert_equal Beer, results[0].class
assert_equal "Todd Enders' Witbier", results[0].name
```

As you can see above, the `store` method takes a new instantiated `Beer` instance; what's more, I can use my `search_ingredients_for` class method to find a corresponding document.

While there are alternate Ruby [Elasticsearch](http://thediscoblog.com/blog/2013/01/02/scalable-searching-with-elasticsearch/) libraries available, Tire is by far the richest -- its features along with its DSL make working with [Elasticsearch](http://www.ibm.com/developerworks/library/j-javadev2-24/) easy along with fun. So what are you waiting for? Require Tire to inquire! 
