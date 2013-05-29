---
layout: post
title: "Callbacks in Appcelerator Titanium modules"
date: 2013-05-29 13:42
comments: true
categories: [mobile, JavaScript, Java, iOS, Objective-C]
---

{% img right /images/mine/appc_stacked.png %}I recently found myself implementing both an Android and iOS [Appcelerator](http://www.appcelerator.com/) module for [App47](http://www.app47.com/)'s [respective Agent libraries](https://github.com/App47/appcelerator-modules). Like [PhoneGap plugins](https://github.com/App47/phonegap-plugins), [Appcelerator modules](https://wiki.appcelerator.org/display/tis/Using+Titanium+Modules) are a way to bridge an Appcelerator app with native code running on a device; in this case, the native code happens to be App47's [Android](http://app47.com/wiki/doku.php?id=configure:androidapp) and [IOS](http://app47.com/wiki/doku.php?id=configure:iosapp) Agents, which capture [usage analytics](http://www.app47.com/features/) and facilitate a few security features. Naturally, these Agent libraries are  coded in Java and Objective-C.

In the end, what I wanted to implement was a [JavaScript-ish callback](http://recurial.com/programming/understanding-callback-functions-in-javascript/) associated with a native App47 Agent call. Alas, it took me a lot of digging to achieve this goal. 

<!-- more -->

For example, for a timed event (which, as you've probably guessed, captures how long an event took), rather than the more traditional call which is inline:

``` javascript Straightforward method invocation
var id = agent.startTimedEvent("openCrust 2.0.27");
openCrust({});
agent.endTimedEvent(id);
```

I wanted a more JavaScript friendly call that wraps the timed code like so:

``` javascript Callback invocation
agent.timedEvent("openCrust 2.0.27", function() {
  openCrust({});
});
```

This has the benefit of wrapping a desired event -- there is no explicit need for anyone to code the ending -- it is automatically done via the `timedEvent` call after invoking the passed in function.

The Titanium module documentation is a bit hard to find (that is, finding up-to-date valid documentation is challenging); your best bet to see how to do something interesting is to look at the [various code](https://github.com/appcelerator/titanium_mobile/tree/master/android/modules) repositories [on Github](https://github.com/appcelerator/titanium_modules) followed by studying the API docs (i.e. JavaDocs and [.h/.m files for iOS](https://github.com/appcelerator/titanium_mobile/blob/master/iphone/Classes/KrollCallback.m)). 

It turns out, invoking a JavaScript callback in either Android or iOS is fairly straightforward. In the case of Android, you need to use the `KrollFunction` type like so:

``` java Wrapped Timed Event
@Kroll.method
public void timedEvent(String name, KrollFunction callback) {
  String id = EmbeddedAgent.startTimedEvent(name);
  callback.call(getKrollObject(), new HashMap<String, String>());
  EmbeddedAgent.endTimedEvent(id);
}
```

As you can see in the above code, I'm not doing anything special like passing in any arguments to the `KrollFunction` instance. If you want to do that, say in the case of passing in some special value that the corresponding callback will use, then you can either pass in a `Map` or an `Object[]`. 

For example, you can implement this style of callback where a custom value is passed in for a timed event like so:

``` java Callback with Java
@Kroll.method
public void startTimedEvent(String name, KrollFunction callback) {
  String id = EmbeddedAgent.startTimedEvent(name);
  HashMap<String, String> map = new HashMap<String, String>();
  map.put("id", id);
  callback.call(getKrollObject(), map);
}
```

This results in a JavaScript call like so:

``` javascript Using a callback but not wrapping the event
agent.startTimedEvent("openCrust 2.0.27", function(result) {
  var id = result['id'];
  openCrust({});
  agent.endTimedEvent(id);
});
```

In iOS land, invoking a callback is a bit different, but certainly as easy. The same `timedEvent` JavaScript method that takes a callback to be wrapped by the timed event can be implemented as follows:

``` objective-c Wrapped event by callback
- (void)timedEvent:(id)args
{
  NSString* eventName = [args objectAtIndex:0];
  NSString* eventID = [EmbeddedAgent startTimedEvent:eventName];
    
  KrollCallback* callback = [args objectAtIndex:1];
  if(callback){
    [callback call:nil thisObject:nil];
  }
    
  [EmbeddedAgent endTimedEvent:eventID];
}
```

In this case, I'm using the `KrollCallback` type, which appears to be analogous to Appcelerator's Android `KrollFunction`. 

If you need to pass values to a corresponding JavaScript function, they need to be in `NSArray` form, thus, you can do something like this to pass in parameters to the underlying JavaScript function:

``` objective-c With callback parameter
- (void)startTimedEvent:(id)args
{
  NSString* eventName = [args objectAtIndex:0];
  NSString* eventID = [EmbeddedAgent startTimedEvent:eventName];
    
  KrollCallback* callback = [args objectAtIndex:1];
  if(callback){
    NSDictionary* dict = [[[NSDictionary alloc] initWithObjectsAndKeys:@"id", eventID, nil] autorelease];
    NSArray* array = [NSArray arrayWithObjects: dict, nil];
    [callback call:array thisObject:nil];
  }
}
```

As you can see in the above code, a callback instance takes as a parameter an `NSArray`, thus, I have to covert my `NSDictionary` into an array via Objective-C's handy `arrayWithObjects` function.

The default module examples [provided by Appcelerator naturally work](http://docs.appcelerator.com/titanium/latest/#!/guide/Titanium_Mobile_Module_API), but alas, the non-callback style of invocation was less than appealing, especially if you are going to be coding an [Appcelerator](http://en.wikipedia.org/wiki/Appcelerator_Titanium) app in JavaScript. Nevertheless, you can do it easily enough provided you are willing to dig through myriad repositories...or you could save yourself the headache and read this blog post. 
