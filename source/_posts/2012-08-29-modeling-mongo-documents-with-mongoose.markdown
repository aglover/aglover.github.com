---
layout: post
title: "Modeling Mongo documents with Mongoose"
date: 2012-08-29 21:45
comments: true
categories: [Node, MongoDB, JavaScript]
---

Without a doubt, one of the quickest ways to build an application that leverages [MongoDB](http://www.mongodb.org/) is with Node. It's as if the two platforms were made for each other; the sheer number of Node libraries available for dealing with Mongo is testimony to a vibrant, innovative community. Indeed, one of my favorite Mongo focused libraries these days is [Mongoose](http://mongoosejs.com/). 

Briefly, Mongoose is an object modeling framework that makes it incredibly easy to model collections and ultimately work with intuitive objects that support a rich feature set. Like most things in Node, it couldn't be any easier to get set up. Essentially, to use Mongoose, you'll need to define `Schema` objects -- these are your documents -- either top level or even embedded. 

For example, I've defined a `words` collection that contains documents (representing...words) that each contain an embedded collection of `definition` documents. A sample document looks like this:

``` javascript Word Document
{  
  _id: "4fd7c7ac8b5b27f21b000001", 	
  spelling: "drivel", 
  synonyms: ["garbage", "dribble", "drool"], 
  definitions: [ 
  { part_of_speech: "noun",
    definition:"saliva flowing from the mouth, or mucus from the nose; slaver."
  }, 
  { part_of_speech: "noun", 
    definition:"childish, silly, or meaningless talk or thinking; nonsense; twaddle." 
  }]
}
```
 
From an document modeling standpoint, I'd like to work with a `Word` object that contains a list of `Definition` objects and a number of related attributes (i.e. synonyms, parts of speech, etc). To model this relationship with Mongoose, I'll need to define two `Schema` types and I'll start with the simplest: 

``` javascript Definition Schema
Definition = mongoose.model 'definition', new mongoose.Schema({
  part_of_speech : { type: String, required: true, trim: true, enum: ['adjective', 'noun', 'verb', 'adverb'] },
  definition : {type: String, required: true, trim: true}
})
```

As you can see, a `Definition` is simple -- the `part_of_speech` attribute is an enumerated `String` that's required; what's more, the `definition` attribute is also a required `String`.

Next, I'll define a `Word`:

``` javascript Word Schema
Word = mongoose.model 'word', new mongoose.Schema({
  spelling : {type: String, required: true, trim: true, lowercase: true, unique: true},
  definitions : [Definition.schema],
  synonyms : [{ type: String, trim: true, lowercase: true }]
})
``` 

As you can see, a `Word` instance embeds a collection of `Definition`s. Here I'm also demonstrating the usage of `lowercase` and the index `unique` placed on the `spelling` attribute.

To create a `Word` instance and save the corresponding document couldn't be easier. Mongo array's leverage the `push` command and Mongoose follows this pattern to the tee. 

``` javascript saving with Mongoose
word = new models.Word({spelling : 'loquacious'})
word.synonyms.push 'verbose'
word.definitions.push {definition: 'talking or tending to talk much or freely; talkative; \
  chattering; babbling; garrulous.', part_of_speech: 'adjective' }
word.save (err, data) ->
```

Finding a word is easy too:

``` javascript findOne in action
it 'findOne should return one', (done) ->
  models.Word.findOne spelling:'nefarious', (err, document) ->
    document.spelling.should.eql 'nefarious'
    document.definitions.length.should.eql 1
    document.synonyms.length.should.eql 2
    document.definitions[0]['part_of_speech'].should.eql 'adjective'
    done(err)
```

In this case, the above code is a [Mocha](http://visionmedia.github.com/mocha/) test case (which uses [should](https://github.com/visionmedia/should.js) for assertions) that demonstrates Mongoose's `findOne`. 

You can find the code for these examples and more at my Github repo dubbed [Exegesis](https://github.com/aglover/exegesis) and while you're at it, check out the [developerWorks videos I did for Node](http://www.ibm.com/developerworks/training/kp/j-kp-node/index.html)!