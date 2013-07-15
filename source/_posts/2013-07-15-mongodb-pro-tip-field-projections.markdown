---
layout: post
title: "MongoDB pro tip: field projections"
date: 2013-07-15 15:07
comments: true
categories: [MongoDB, Mongoid]
---


{% img right /images/mine/mongodb_icon.png %}Did you ever learn that ```select * from table``` in RDBMS-land is bad? Of course, you did! If you're only looking for the email address of a user and not the other 15 columns worth of data, then why ask for that data and incur a penalty? The query ```select email from user where user_id = 1;``` is far more efficient for the database and the corresponding application that issued it, because there is _less data to fetch and consume_. 

<!-- more -->

As it turns out, the same rule of thumb is true in [MongoDB-land][0]. That is, ```db.users.find({user_id:1})``` is just as inefficient as the ```select *``` query if all you want is the user's email address. With MongoDB, you can specify a [projection][2] as a part of your query that ultimately can limit what fields come back. 

Thus, if I only want the ```email``` field on a user, I can issue a query like so: 

``` javascript MongoDB field projection
db.users.find({user_id:1}, {email:1})
```

The second clause specifies that you only want the ```email``` field returned. You can also negate fields by issuing a 0, which means false. To negate ```first_name``` and ```last_name```, you would type:

``` javascript MongoDB field negation with 0 or false
db.users.find({user_id:1}, {first_name:0, last_name:0})
```

In this case, I'd get all fields on that user document _except_ ```first_name``` and ```last_name```. Note, you cannot issue both an include and exclude in the same statement. 

For you [Mongoid][1] users, including specific fields translates to ```only``` and excluding them translates to ```without``` -- each clause can be attached to a criteria (but not both at the same time). For example, if ```User``` is a Mongoid document and I only want an underlying query to grab the ```email``` field, then the corresponding Mongoid query would be:

``` ruby Mongoid field projection
User.where(user_id:1).only(:email)
```

Field projection reduces document sizes on a fetch -- this decreases memory consumption (for example, in the case of Mongoid your models aren't fully populated with data) as well as bandwidth (that is, document retrieval is faster). Both MongoDB and the calling application benefit from field projection -- it's a win-win all the way around. 


[0]: http://www.mongodb.org/
[1]: http://mongoid.org/en/mongoid/index.html
[2]: http://docs.mongodb.org/manual/reference/method/db.collection.find/


