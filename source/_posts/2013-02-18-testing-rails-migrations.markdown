---
layout: post
title: "Testing Rails migrations"
date: 2013-02-18 16:22
comments: true
categories: [Rails, Ruby]
---

I recently found myself searching [Stackoverflow](http://stackoverflow.com/) and Google for various techniques for automatically testing [Rails migrations](http://guides.rubyonrails.org/migrations.html). I was surprised not to have found [too much](http://stackoverflow.com/questions/6079016/how-do-i-test-rails-migrations) information though. While testing a migration is fairly straightforward (migrations are classes and you can easily invoke corresponding methods); the challenge can be setting models up properly. That is, migrations are a tool to update an underlying database to reflect changes in models -- by the time you are writing a migration, the models _already_ reflect what should be. 

Accordingly, to test a migration, you need to set up your test with how things were and then run your migration and verify things have migrated.  As it turns out, this is super easy to do with a document oriented database like [MongoDB](http://www.mongodb.org/). For this particular project, we're using [Mongoid](http://mongoid.org/en/mongoid/index.html),  which is an Object-Document-Mapper (or ODM) for MongoDB and we're also using a nifty gem dubbed [mongoid_rails_migrations](https://github.com/adacosta/mongoid_rails_migrations), which facilitates writing Mongoid migrations. 

In order to reflect the state of the underlying datastore before running a migration, I needed to remove a particular collection, which due to changes in our models, is automatically populated with meta data when various events occur (think [relational trigger](http://en.wikipedia.org/wiki/Database_trigger) here, for example). As you can probably see, as this new collection doesn't exist in production, all the existing data in production needs some corresponding default meta data to reflect the new requirements which brought this collection to life. 

Accordingly, once I initialize my models and the corresponding collection is populated, I need to completely [drop the collection](http://docs.mongodb.org/manual/reference/method/db.collection.drop/). Then, I can run my migration and then verify that the previously nuked collection is present with all the required data.  

In my case, I'm using [shoulda](https://github.com/thoughtbot/shoulda) in concert with [Test::Unit](http://ruby-doc.org/stdlib-1.9.3/libdoc/test/unit/rdoc/Test/Unit.html), but the details of a test framework really don't matter -- in any case, you'll need to load your migration, which can be done via a `require` statement like so:

``` ruby Requiring a migration
require File.join(Rails.root, 'db', 'migrate', '20130217194234_member_app_roles_permissions')
```

In my [fixture](http://en.wikipedia.org/wiki/Test_fixture) logic, I've initialized a few objects and related them, which automatically creates the aforementioned meta data collection; consequently, I need to do two things. First, get a connection to the underlying test datastore and then drop that collection. 

``` ruby Obtaining a connection and dropping a collection
db = @account.db
db['member_app_roles'].drop
```

Now I'm ready to run my migration -- it's as simple as invoking the [class method](http://www.railstips.org/blog/archives/2009/05/11/class-and-instance-methods-in-ruby/) `up`!

``` ruby Invoking a migration 
MemberAppRolesPermissions.up
```

Once the migration has finished running, I can then verify that everything is cool and copasetic. In this case, I need to go directly to the datastore because those objects in memory won't reflect my changes just yet. 

``` ruby Asserting things worked!
member = Member.find(member.id)

assert_equal 2, member.member_app_roles.size
member.member_app_roles.each do |app_role|
  assert_equal 'admin', app_role.role
  assert_equal member, app_role.member
  assert_not_nil app_role.app
end
```

As a benefit of thinking through how to test a migration, you end up unveiling how you'd construct the migration's `down` method. That is, in my case, to roll back, all I need to do is blow away the `member_app_roles` collection!

As you can see, testing migrations isn't terribly difficult (probably a good reason why I haven't found too much information about it); the key aspect is the logic that is applied to verify your migration actually worked. It should be noted that while I executed this test in the context of a document oriented database, you could certainly do the same in a relational database. For example, by dropping  columns, tables, etc before running a migration. Either way, testing a migration is a snap. Can you dig it? 