---
layout: post
title: "Docker containers with Gradle in 4 steps"
date: 2014-06-13 11:52
comments: true
categories: [Java, Docker, Linux, DevOps, Ubuntu]
---

{% img right /images/mine/fourdoor.jpg %} Do you need to create a [Docker][0] image from your Java web app? Are you using [Gradle][6]? If so, then you are only 4 steps away from Docker nivana. 

For this example, I'm going to use a simple [Spring Boot][4] application. You can find all the source code in my [Github repository dubbed galoshe][1]. 

If you haven't had a chance to see Spring Boot in action, then you're in for a treat, _especially_ if the words _simple_ and _Java web app_ in the same sentence make you flinch. That was certainly my long standing reaction until I took a serious look at Boot. 

<!-- more --> 

For instance, a quick and dirty "hello world" Boot app is essentially more imports & annotations than actual code. Check it out: 

```java A simple Spring Boot application
package com.github.aglover.galoshe;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

  public static void main(String[] args) {
    ApplicationContext ctx = SpringApplication.run(Application.class, args);
  }

  @RequestMapping("/")
  public String index() {
    return "Hello to you, world";
  }
}
```

Running this application is as easy as typing: 

```bash
$ java -jar build/libs/galoshe-0.1.0.jar
```

That command will fire up an embedded web container with the request path `/` mapped to return the simple `String` "Hello to you, world". You can define what port this application will run on via an `application.properties` file like so:

```java application.properties
server.port: 8080
```

Consequently, if I take my browser and point it to localhost:8080, I see the pedestrian, but oh-so-gratifying-when-you-see-it salutation. 

Now that you've been introduced to the application I'd like to distribute as a Docker container, let me show you how to do it in 4 easy steps. 

Keep in mind, however, that in order to use the gradle-docker plugin I use in this example, you'll need to have Docker installed as the plugin shells out to the `docker` command.

#### Step 1: Apply some plugins

First and foremost, to Docker-ize your application, you'll need to use two Gradle plugins: `docker` and `application`. 

The [gradle-docker plugin][2] by [Transmode][9] is actually 1 of 2 available plugins for Dockering with Gradle. The [other plugin][3] by [Ben Muschko][7] of [Gradleware][8] is a bit more advanced with additional features, however, I find the Transmode plugin the easiest and quickest to get going. 

The `application` plugin is actually included _automatically_ via the `spring-boot` plugin in my particular example, however, if you aren't using Boot, then you'll need to add the following two plugins to your `build.gradle` file: 


```java
apply plugin: 'application'
apply plugin: 'docker'
```

As the `docker` plugin is a 3rd party plugin, you'll need to tell Gradle how to find it via a `dependencies` clause. 

```java Specifying the classpath for the docker plugin
buildscript {
    repositories { mavenCentral() }
    dependencies {
        classpath 'se.transmode.gradle:gradle-docker:1.1'
    }
}
```
Now your Gradle script is ready to start Docker-ing. Next up, you'll need to provide some clues so the plugin can create a valid [`Dockerfile`][5].  

#### Step 2: Provide some properties

The gradle-docker plugin doesn't directly create a Docker container -- it merely creates a `Dockerfile` and then shells out to the `docker` command to build an image. Consequently, you need to specify a few properties in your `build.gradle` file so that the corresponding `Dockerfile` builds a valid container that automatically runs your application. 

You need to provide:

 * The class to run i.e. the class in your application that contains a `main` method
 * The target JVM version (default is Java 7)
 * [Optionally][11], a group id, which feeds into the corresponding Docker tag. 

Accordingly, my `build.gradle` defines all three properties like so: 

```java Defining properties for the docker plugin
group = 'aglover'
sourceCompatibility = 1.7
mainClassName = 'com.github.aglover.galoshe.Application'
```

A few notes about these properties. Firstly, [Java 8][11] isn't _[currently][12]_ available for this plugin. If you don't specify a `sourceCompatibility`, you'll get Java 7. Next, the `group` property isn't required; however, it helps in Docker tagging. For example, my project's `baseName` is dubbed `galoshe`; consequently, when the plugin creates a Docker image, it'll tag that image with the pattern `group/name`. So in my case, the corresponding image is tagged `aglover/galoshe`. 

Finally, the `mainClassName` shouldn't be too surprising - it's the hook into your application. In truth, the plugin will create a script that your resultant Docker image will invoke on startup. That script will essentially call the command: 

```bash
java -classpath your_class_path your_main_class
```

At this point, you are almost done. Next up, you'll need to specify any `Dockerfile` instructions.

#### Step 3: Specify any required Dockerfile instructions

`Dockerfile`s contain specialized instructions for the corresponding image they create. There [are a few important ones][5]; nevertheless, my Boot app only requires one: `port`, which is set via the `exposePort` method of the plugin. 

Consequently, to ensure my Docker container exposes port 8080 as defined in my `application.properites` file, I'll add the following clause to my `build.gradle` file: 

```java Specifying port 8080
distDocker {
    exposePort 8080
}
```

A few other aspects you can muddle with via the plugin are `addFile` which results in an `ADD` instruction, `runCommand`, which results in a `RUN` instruction, and finally `setEnvironment`, which creates an `ENV` instruction. 

Now you're done with your Gradle build. All that's left to do is run your build and fire the image up! 


#### Step 4: Build and run it

Provided you've configured the gradle-plugin properly, all that's left to do is run your build. In this case, the command is simply `distDocker`. 

```bash Running my build 
$ ./gradlew distDocker
```

The first time you run this command it'll take a bit as various images will be downloaded. Subsequent runs will be lightning quick though. 

After your build completes, your image will be created with the tag I noted earlier. In my case, the tag will be `aglover/galoshe`, which I can quickly see by running the `images` command:


```bash Listing available local Docker images
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
aglover/galoshe     latest              332e163221bc        20 hours ago        1.042 GB
dockerfile/java     latest              f9793c257930        3 weeks ago         1.026 GB
```

I can subsequently run my image like so:

```bash Running my container
docker run 332e163221bc
```

I can naturally go to my browser, hit localhost:8080 and find myself quite satisfied that my image runs a nifty greeting. 

Of course, I would need to [publish this image][13] for others to use it; nevertheless, as you can see, the gradle-plugin allows me to quickly create Docker containers for Java apps. 

[0]:http://www.docker.com/
[1]:https://github.com/aglover/galoshe
[2]:https://github.com/Transmode/gradle-docker
[3]:https://github.com/bmuschko/gradle-docker-plugin
[4]:http://projects.spring.io/spring-boot/
[5]:http://thediscoblog.com/blog/2014/05/05/dockerfiles-in-a-jiffy/
[6]:http://www.gradle.org/
[7]:https://github.com/bmuschko
[8]:http://www.gradleware.com/
[9]:https://github.com/Transmode
[10]:https://github.com/Transmode/gradle-docker/issues/8
[11]:http://thediscoblog.com/blog/2014/03/25/java-8-s-functional-fomentation/
[12]:https://github.com/Transmode/gradle-docker/pull/9
[13]:https://hub.docker.com/u/aglover/