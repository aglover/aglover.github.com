---
layout: post
title: "The Rails, CloudFront, and Heroku performance hat-trick"
date: 2013-05-01 09:10
comments: true
categories: [Ruby, Heroku, AWS, Rails]
---

{% img right /images/mine/hat-trick.png %}[Amazon CloudFront](http://aws.amazon.com/cloudfront/) is a pay-as-you-go global [content delivery network](http://en.wikipedia.org/wiki/Content_delivery_network) (or CDN) that provides high availability and high performance serving of static assets. Basically, it means users have to wait less time to view your web app regardless of their location on the globe. 

It's easy to configure a [Rails](http://rubyonrails.org/) app to take advantage of CloudFront; what's more, if your Rails app is hosted on [Heroku](http://www.ibm.com/developerworks/java/library/j-javadev2-21/), there's [a nifty gem](https://github.com/romanbsd/heroku-deflater), dubbed heroku-deflater, that'll enable [HTTP compression](http://en.wikipedia.org/wiki/HTTP_compression) of static assets ([other than images](http://stackoverflow.com/questions/12326191/any-way-to-serve-gzip-assets-from-heroku)).

To get these three entities to play nicely together requires a few simple steps. Let me show you how. 

<!--more-->

First, if you want to enable HTTP gzip compression of static assets other than images from a Heroku app, then add the heroku-deflater gem to your `Gemfile`. This gem doesn't compress images as in some cases, zipping images creates bigger ones! 

Once you've run `bundle install` and deployed your app to Heroku, fire up a terminal and run [cURL](http://thediscoblog.com/blog/2013/04/18/curling-for-wget/) to verify that the HTTP response Content-Encoding is gzip like so:

``` bash cURL testing gzip response
curl -i -H "Accept-Encoding: gzip,deflate" http://your.awesome.web.app
```

You should see in the response this key phrase:

``` bash cURL response
Content-Encoding: gzip
```

If you do see the Content-Encoding set to gzip, then you are good to go. If, for some reason, you don't see it, check your environment's configuration file (which you will have to edit to get CloudFront working anyway) and verify that the property `config.serve_static_assets` is set to `true`.

Next, sign into the AWS Management Console and enable CloudFront if you haven't already. From the CloudFront admin page, create a new distribution via the Create Distribution button on the top left.

{% img center /images/mine/cloudfront_1.png %}

Once the create distribution wizard begins, be sure to select Download as your delivery method. 

{% img center /images/mine/cloudfront_2.png %}

In the next screen, there are some important fields you'll need to fill out, namely: the Origin Source Name and the Viewer Protocol Policy. For the Origin Source Name, you will need to put in your app's URL or the Heroku URL (if you do not map a custom domain name to it). If you web site supports HTTPS, then be sure to set the Viewer Protocol Policy to HTTP and HTTPS.

{% img center /images/mine/cloudfront_3.png %}

The only other important setting after these two is the Price Class. It's here where you can set where CloudFront will essentially serve up your content -- the default setting of Use All Edge Locations is most likely what you need. 

{% img center /images/mine/cloudfront_5.png %}

Finally, click the Create Distribution button -- once you do that, it'll take a bit for things to initialize (basically, the CDN needs to get built and this may take up to 30 minutes). 

Now to configure your Rails app, you'll need to open up your target environment's configuration file (i.e. `production.rb`). The [two fields](http://bindle.me/blog/index.php/395/caches-cdns-and-heroku-cedar) you'll want to be sure are properly set are `serve_static_assets` and `static_cache_control`. In particular, you are setting the cache control variable to one year. This means that once a static asset, like a JavaScript file is downloaded to the browser, it'll be cached for one year. Don't fret, however, if you think that'll inhibit change -- the file that is ultimately downloaded has a hash attached to it (via the magic of [Rail's Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html)). Consequently, the file that is cached is something like `your_js_file-asdf098203820980a980` where that last bit is a hash value that'll change if the file itself changes. 


``` ruby production.rb edited to support CDN
config.serve_static_assets = true
config.static_cache_control = 'public, max-age=31536000'
```

The last change you need to make to your environment file is to set the `asset_host` to the CloudFront domain that you just created. You can find this in your AWS Management Console -- it'll be a cryptic URL like http://asdjlkj2321.cloudfront.net. 

``` ruby production.rb edited to support asset host
config.action_controller.asset_host = 'the domain name from AWS Dashboard'
```

Commit your changes and deploy your app. 

To verify things are kosher, you'll need to give it some time (check the status of your CloudFront CDN -- if it's status is Enabled then you are good to go!). If things are ready, then fire up a browser and go to your app. 

In this case, [I'm using Chrome](http://thediscoblog.com/blog/2013/04/15/chromes-console-commands/). Go to JavaScript console and hit the Network tab. 

{% img center /images/mine/cloudfront_6.png %}

Surf around and you'll note a few things -- one, that the assets like images and JavaScript files are being severed up from your CDN (just look at the URL) and that the size will often say "(from cache)" -- that means the CDN is handling the load rather than [Heroku](http://www.ibm.com/developerworks/podcast/glover-heroku-110811/). You should also note that your web app is probably a bit more snappy!

{% img center /images/mine/cloudfront_7.png %}

Check out your Heroku logs and you'll note fewer hits in this case -- dynamic pages are still being loaded, however, static assets are not anymore -- that's the job of your CDN! 

CloudFront isn't free; nevertheless, I think you'll find the corresponding cost quite reasonable. Pricing will vary depending on how much content you'll be serving up with CloudFront -- this is a function of how many visitors you have _along with_ how many static assets that are ultimately downloaded to a user's browser. For instance, 500GB of average content will cost you less than $75/month. 

CloudFront's pay-as-you-go model makes it extremely affordable to add a nice bit of pep to your app's performance along with using gzip compression and HTTP caching. And hopefully as I've shown you, it's rather easy to do with a Rails app running on Heroku. 
