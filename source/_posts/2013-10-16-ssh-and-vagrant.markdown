---
layout: post
title: "SSH &amp; Vagrant"
date: 2013-10-16 15:55
comments: true
categories: [Linux, Heroku]
---


[Vagrant](http://www.vagrantup.com/) is a handy tool for creating VMs. It's a lot like firing up an [EC2](http://thediscoblog.com/blog/categories/aws/) instance, but in Vagrant's case, everything is localized. And best of all, it's free. 

I tend to favor [Ubuntu](https://github.com/aglover/ubuntu-equip) as my preferred flavor of linux; consequently, all production EC2 instances use a customized Ubuntu AMI. Testing various aspects of this system with various software libraries, however, is initially tested locally using Vagrant VMs. What's more, you can install localized VMs of [other operating systems](http://www.vagrantbox.es/) ranging from Debian to OpenSuse to [Heroku's Cedalon](https://devcenter.heroku.com/articles/stack). 

<!-- more -->

Firing up a local instance of [Heroku](http://thediscoblog.com/blog/categories/heroku/)'s Cedalon (which is a version of Ubuntu 10.04 with Ruby and Node.js installed) is as easy as typing:

``` bash Firing up Heroku's Cedalon locally
vagrant init heroku http://dl.dropbox.com/u/1906634/heroku.box
vagrant up
```

Then you can SSH to that running VM like so:

``` bash SSHing to a local VM
vagrant ssh
```

The command `vagrant ssh` can be problematic, though, especially if you are automating some aspect of SSH -- for example, you are using some library on top of SSH. In that case, you need to use SSH _directly_ rather than through Vagrant. 

Luckily, you can SSH normally to local Vagrant instances easily enough. You'll need to tell SSH which key to use (Vagrant creates one for you), which user to connect as (usually vagrant), and what port to connect to (usually 2222).  All of this information can be found via the command `vagrant ssh-config`. 

To use plain Jane SSH when working with Vagrant instances, you'll first need to execute this command:

``` bash Printing out the location of Vagrant's key
vagrant ssh-config | grep IdentityFile  | awk '{print $2}'
```

This [3 pronged command](https://groups.google.com/forum/#!topic/vagrant-up/B5WIfDcIRtE) ultimately tells you where Vagrant has created a key file. The command [`awk '{print $2}'`](http://www.thegeekstuff.com/2010/01/awk-introduction-tutorial-7-awk-print-examples/) prints the second column of the line in the `ssh-config` string that starts with `IdentityFile` (which points to its location). 

Now that you know where the key file is, you can ssh (assuming the default port hasn't changed from 2222 -- double check your `ssh-config` to make sure) like so:

``` bash Using normal SSH
ssh -i /some/dir/.vagrant.d/insecure_private_key -l vagrant -p 2222 127.0.0.1 
```

If you happen to fire up different VMs -- for example, I have both Heroku Cedalon and normal Ubuntu instances, SSH will probably complain that the remote host identification has changed. This is a valid warning as the RSA fingerprint associated with the local host has indeed changed. 

You can suggest that [SSH ignore this warning](http://linuxcommando.blogspot.com/2008/10/how-to-disable-ssh-host-key-checking.html) by adding two additional flags. I highly recommend you only do this via the command line and _not with some SSH config file_ as the warning SSH throws is completely legitimate and could be saving you from a man-in-the-middle attack.

Nevertheless, if you are working with localized VMs and need to bypass that warning, use these two flags:

``` bash Two additional flags to SSH
-o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no
```

Consequently, when I SSH to a local Vagrant instance, I type something along the lines of:

``` bash SSHing to Vagrant instances
ssh -i /some/dir/.vagrant.d/insecure_private_key -l vagrant -p 2222 -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no 127.0.0.1
```

Finally, to shut down a Vagrant instance, just issue the command:

```
vagrant destroy
```

Vagrant makes it super easy to fire up localized development environments -- Vagrant instances boot up faster than EC2 instances and they're free. And now you know how to use normal SSH to connect to them. Can you dig it?