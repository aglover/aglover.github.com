---
layout: post
title: "Provisioning Ubuntu with Java in 3 steps"
date: 2013-11-18 17:34
comments: true
categories: [Ubuntu, Java, DevOps, Linux]
---

{% img right /images/mine/ubuntu.png %} [As I've written about before](http://thediscoblog.com/blog/2013/10/16/ssh-and-vagrant/), [Vagrant](http://www.vagrantup.com/) is handy tool for creating [localized VMs](http://www.vagrantbox.es/). It's a lot like firing up [EC2](http://thediscoblog.com/blog/categories/aws/) images, but, for the most part, things are localized (you can, by the way, use Vagrant to [fire up EC2 images](https://github.com/mitchellh/vagrant-aws)). If you've ever used VMWare before, its the same thing, except Vagrant is free. You can create VMs of various operating systems, fire them up, and tear them down all with ease. 

Vagrant plays nicely with hip DevOps frameworks like [Chef](http://www.opscode.com/chef/) and [Puppet](http://puppetlabs.com/) and if your installations require a number of components, then these tools are defiantly the way to go. Sometimes, however, a simple Bash script is good enough as in the case for auto-installing some base component, like [Java](http://thediscoblog.com/blog/categories/java/), [Node.js](http://thediscoblog.com/blog/categories/node/) or [Ruby](http://thediscoblog.com/blog/categories/ruby/). 

Using Vagrant's configuration file, aptly dubbed `Vagrantfile`, you can instruct a VM instance to run a series of steps -- these steps can be simple shell scripts, Chef cookbooks, or the analogous Puppet equivalent. 

<!-- more -->

Accordingly, the first step to provision an Ubuntu box with Java is to initialize a 64-bit [Ubuntu](http://thediscoblog.com/blog/categories/linux/) 12.04 LTS ([Precise Pangolin](https://wiki.ubuntu.com/PrecisePangolin)) instance. You can do this via the `vagrant init` command like so:

``` bash Initializing a Vagrant box
$> vagrant init ubuntu.lts.64 http://files.vagrantup.com/precise64.box
```

This creates a `Vagrantfile` in the directory where you ran the command and creates a named VM (i.e. "ubuntu.lts.64") that is based off of Ubuntu 12.04 LTS. 

Base Ubuntu installations do not come with Java; if you'd like to install a particular JDK, say Oracle's JDK 7, you can leverage [ubuntu-equip](https://github.com/aglover/ubuntu-equip), which is series of Bash scripts that install various components like Java, Node.js, MongoDB, Redis, Ruby, etc. 

Thus, for step 2, open up the newly created `Vagrantfile` and you should see two lines like so: 

``` ruby A basic VagrantFile contains the box and box_url attributes
config.vm.box = 'ubuntu.lts.64'
# a few comments...
config.vm.box_url = 'http://files.vagrantup.com/precise64.box'
```

After the `vm.box_url` declaration, insert the following line:

``` ruby Installing Java
config.vm.provision :shell, inline: 'wget --no-check-certificate https://github.com/aglover/ubuntu-equip/raw/master/equip_java7_64.sh && bash equip_java7_64.sh'
```

This command instructs the instance to run an inline Bash command once it is up and running, which in this case auto-installs Oracle's Java 7 JDK (see the [ubuntu-equip](https://github.com/aglover/ubuntu-equip) project for more information). 

Save your `VagrantFile` and then, for step 3, run the following command in the same directory:

``` bash Firing up a new VM
$> vagrant up
```

If this is the first time firing up this particular VM, you should see some text indicating that a particular box is being downloaded. Once the download is complete, the instance will boot up and subsequently invoke the inline provision command that kicks off the installation of Java. 

If all goes well, you should see a lot of text scroll by ending with:

``` bash Java is installed!
java version "1.7.0_25"
Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)
```

And that's it. To use the VM, [simply SSH to it](http://thediscoblog.com/blog/2013/10/16/ssh-and-vagrant/). Go ahead and type `java -version` just to convince yourself. Go ahead, I'll wait for you...there, are you happy now?  Wasn't that easy? Provisioning Ubuntu VMs with Vagrant couldn't be any easier with [ubuntu-equip](https://github.com/aglover/ubuntu-equip), dig it?