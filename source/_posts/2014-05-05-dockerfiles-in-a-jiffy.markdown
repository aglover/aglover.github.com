---
layout: post
title: "Dockerfiles in a jiffy"
date: 2014-05-05 10:19
comments: true
categories: [Docker, Java, Linux, AWS, DevOps, Ubuntu]
---

{% img left /images/mine/docker.png %}[Docker][0] is a lightweight container for applications -- think of a Docker as an app in a box, except that the box in this case isn't an entire VM, but the bare necessities required to run a process. Consequently, you can run many Dockers in a VM. In essence, Docker replaces installation steps for a particular app. Rather than having to execute a series of steps to get, say, [MongoDB][3] running, you can simply fire up a Mongo Docker image. 

Docker images can be created from a `Dockerfile`, which is similar to a `Vagrantfile` or even a build script -- it's a prescription for how to assemble an image. You don't need to have a `Dockerfile` to create a Docker image, however, creating one makes image creation _repeatable_. It also provides a means for others to verify an image. 

<!-- more -->

There are a few key instructions you should be aware of when creating `Dockerfiles` -- mainly,  `FROM`, `RUN`, `EXPOSE`, and `CMD`. To demonstrate how easy this process is, I'm going to create a `Dockerfile` that runs Amazon's DynamoDB Local. 

#### DynamoDB Local

[DynamoDB Local][1] is a simple Java application that emulates [AWS DynamoDB][2]. The benefit of using DynamoDB Local is that you can iterate quickly without using bandwidth against the real DynamoDB and you'll save a few coins in the process. 

Running DynamoDB Local isn't terribly difficult; in fact, provided you have [Java][4] installed, it's as easy as:

``` bash Firing up DynamoDB Local
$ java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar
```

Of course, if you aren't developing a Java application and don't have Java installed, you would need to, of course, install Java. Then you'd need to download the DynamoDB Local package and install it. And then you'd need to run it. 

Alternatively, you could just `pull` a Docker image, `run` it, and get back to work.

#### Creating a Dockerfile

Creating a `Dockerfile` is simple. Fire up your favorite editor and follow along. 

The first required element, `FROM`, indicates the base image or parent from which a docker image is built upon. In many cases, this'll be something like `ubuntu` or `centos`, for example.  In the case of an image for DynamoDB Local, I'm going to base it off _another image_ that already has Oracle's [Java 8][8] installed. That image is based upon [Ubuntu][5]. Note, basing your `FROM` off of another image implies the image is available in an accessible Docker index. You can run your own local or remote indexes or use [Docker's public index][7].

Consequently, the first two lines of my `Dockerfile` are:

``` bash FROM and MAINTAINER elements of a Dockerfile
FROM aglover/java8-pier
MAINTAINER Andy Glover "ajglover@gmail.com"
```

Have a look at my [java8-pier project][6] and you'll see its `Dockerfile` has the line `FROM ubuntu`. The `MAINTAINER` line is self evident. 

The 2nd most important instruction of a `Dockerfile` is `RUN`. Think of `RUN` as `bash` commands required to set up your Docker image. In the case of setting up DynamoDB Local, there are a few things I would like done on the image. First and foremost, I'd like to update base aspects of the underlying [Ubuntu][9] OS via an `apt-get update`. Then I'd like to download the DynamoDB Local archive, however, I'd like to use `wget`, which isn't available on base Ubuntu installs, however. Consequently, I'll install `wget` while I'm at it. 

``` bash apt-get update and wget install
RUN apt-get update -y
RUN apt-get install wget -y
```

Next, I'm going to `wget` the latest version of DynamoDB Local via an AWS URL that points to the latest version (which obviously changes); thus, the `-O` flag forces the downloaded file to the generic name of `dynamo.tar.gz` (rather than something like `dynamodb_local_2014-04-24.tar.gz` where the date can change depending on when AWS releases an update). Finally, after the download completes, the file is moved into a directory dubbed `dynamodb_local`. 

``` bash Downloading the archive
RUN wget http://dynamodb-local.s3-website-us-west-2.amazonaws.com/dynamodb_local_latest -O dynamo.tar.gz
RUN tar xvzf dynamo.tar.gz && mv dynamodb_local_* dynamodb_local
```

Docker images, by default, don't expose any ports through the host on which they are running. You must expose desired ports via the `EXPOSE` command. In my case, DynamoDB Local defaults to port 8000; accordingly, I'll specify in my `Dockerfile` that I wish this port to be open:

``` bash Exposing port 8000
EXPOSE 8000
```

Finally, as I wish to run a service via my Docker image, I need to fire it up! DynamoDB Local is simply a Java process that requires, at a minimum, two parameters. If I were to run DynamoDB Local manually, the corresponding command would be: 

``` bash The command to run DynamoDB Local
$ java -Djava.library.path=./dynamodb_local/DynamoDBLocal_lib -jar ./dynamodb_local/DynamoDBLocal.jar
```

Consequently, to execute this command in a `Dockerfile` I'll need to use the `CMD` instruction (which is probably the most important instruction for creating Dockers!). This instruction takes an array of values -- logically, just take the corresponding manual command and tokenize it by a space and you've got your `CMD`:

``` bash The CMD instruction is important if your Docker image runs a service
CMD ["java", "-Djava.library.path=./dynamodb_local/DynamoDBLocal_lib", "-jar", "./dynamodb_local/DynamoDBLocal.jar"]
```

That's it -- 8 lines and you've got a `Dockerfile` that'll yield Docker image running DynamoDB Local as a service. 

#### Creating images from Dockerfiles

With a `Dockerfile` I can now create an image via the `build` command. Thus, in a terminal window, I'll change directories to where I've saved my `Dockerfile` and run the following:

``` bash Building a Docker image
$ docker build -t aglover/dynamodb-pier . 
```

`aglover/dynamodb-pier` is the desired name of my image. After you run this command, you will see a whole lot of commands executed, including the ones specified in your `Dockerfile`. Once things finish successfully, you should be able to see the resultant image via the `images` command.


``` bash Listing Docker images
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
aglover/dynamodb-pier   latest              aa51ccc5dc3f        14 seconds ago      1.089 GB
aglover/java8-pier      latest              cb8436bb816a        4 weeks ago         1.026 GB
base                    latest              b750fe79269d        13 months ago       175.3 MB
base                    ubuntu-12.10        b750fe79269d        13 months ago       175.3 MB
base                    ubuntu-quantal      b750fe79269d        13 months ago       175.3 MB
base                    ubuntu-quantl       b750fe79269d        13 months ago       175.3 MB
```

I can run my newly minted Docker image via its ID, which, if you look closely in the listing above, is `aa51ccc5dc3f`. 

``` bash Running a Docker image
$ docker run aa51ccc5dc3f
2014-05-05 17:04:14.037:INFO:oejs.Server:jetty-8.1.12.v20130726
2014-05-05 17:04:14.119:INFO:oejs.AbstractConnector:Started SelectChannelConnector@0.0.0.0:8000
```

Note, by default, Docker will run the image in the foreground; accordingly, you can see things are working via the output coming from the DynamoDB Local instance running. 

You can run a Docker image as a daemon via the `-d` flag: 

``` bash Running a Docker image as daemon
$ docker run -d aa51ccc5dc3f
7be2708d94eedaa82432d239659ddb696d66004516174a6e2f79f4ec465eb9fc
```

You can now see what Docker images are running via the `ps` command.

``` bash Docker ps lists running images
$ docker ps
CONTAINER ID        IMAGE                          COMMAND                CREATED             STATUS              PORTS               NAMES
7be2708d94ee        aglover/dynamodb-pier:latest   java -Djava.library.   17 seconds ago      Up 16 seconds       8000/tcp            compassionate_bardeen
```

Finally, you can stop an image via the `stop` command. You must provide the ID of the image, which you can see via a `ps`.

``` bash Stopping a Docker image
$ docker stop 7be2708d94ee
```

Docker has a [public repository][10] can you publish to, provided you have an account. Ultimately, publishing is done via the `push` command. For example, I've published my DynamoDB Local image via:

``` bash Docker pushing
$ docker push aglover/dynamodb-pier
```

Once an image has been published, it can correspondingly be downloaded via the `pull` command: 

``` bash Using a Docker image
$ docker pull aglover/dynamodb-pier
```

Think of `pull`ing as simply downloading and registering a Docker. To use a 'pull'ed Docker, you must still execute the `run` command.

Docker makes it super easy to distribute pre-packaged applications -- rather than installing various binaries on different operating systems (like MongoDB on OSX for development and _a different_ MongoDB binary on Ubuntu for production), you can use the _same_ package across environments. `Dockerfile`s make creating Dockers repeatable. And hopefully I've shown you that crafting a `Dockerfile` isn't terribly difficult.  Go forth and Docker.


[0]:https://www.docker.io/
[1]:http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Tools.DynamoDBLocal.html
[2]:http://aws.amazon.com/dynamodb/
[3]:http://thediscoblog.com/blog/categories/mongodb/
[4]:http://thediscoblog.com/blog/categories/java/
[5]:http://thediscoblog.com/blog/categories/ubuntu/
[6]:https://index.docker.io/u/aglover/java8-pier/
[7]:https://index.docker.io/
[8]:http://thediscoblog.com/blog/2014/03/25/java-8-s-functional-fomentation/
[9]:http://thediscoblog.com/blog/categories/ubuntu/
[10]:http://docs.docker.io/use/workingwithrepository/