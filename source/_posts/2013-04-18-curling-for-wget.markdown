---
layout: post
title: "cURLing for Wget"
date: 2013-04-18 09:33
comments: true
categories: [Linux, OSX]
---

[Wget](http://en.wikipedia.org/wiki/Wget) is an extremely handy utility I use all the time when I find myself on a Linux box. It's quite helpful, for example, for downloading files. Need to [install Ruby](https://github.com/aglover/ubuntu-equip/blob/master/equip_ruby.sh)? No problem, just download the binary like so: 

``` bash wget example of downloading Ruby binary
wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p180.tar.gz
``` 

and you're one step closer. There's no flags to remember either. 

Wget, however, isn't natively available on OSX. From time to time, I'm stung to see the nefarious 'command not found' message after expectantly waiting to see some file I need downloaded. 

Luckily, you can force [cURL](http://curl.haxx.se/docs/manpage.html) to act like Wget with a few flags. Simply use the `-OL` flags like so:

``` bash cURL acting like Wget
curl -OL http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p180.tar.gz
``` 

and you'll be on your way to downloading some file. 

Of course, it's possible to get `wget` on your Mac via [MacPorts or Homebrew](http://osxdaily.com/2012/05/22/install-wget-mac-os-x/); nevertheless, knowing you can achieve the same goals with cURL is always handy.