---
title: Heroku Play Framework Support
slug: play-support
url: https://devcenter.heroku.com/articles/play-support
description: Reference documentation describing the support for the Play Framework on Heroku's Cedar stack.
---

The Heroku Cedar stack natively supports both Play framework 1.2.x, 2.x applications.

This document describes the general behavior of the [Heroku Cedar stack](cedar) as it relates to the recognition and execution of Play framework applications. Play framework applications written in either Java or Scala can be run on Heroku.

For framework specific tutorials visit: 

* [Getting Started with Play! on Heroku/Cedar](play)

You can also find a Play framework template application that can be cloned directly into your Heroku account at [java.heroku.com](http://java.heroku.com)

### Activation

Heroku Play framework support will be applied to applications that match: `*/conf/application.conf` in any directory except for the modules directory

When a deployed application is recognized as a Play application, Heroku responds `-----> Play! app detected` for Play 1.2.x apps, `-----> Play 2.0 - Java app detected` for Play 2.0.x Java apps, or `-----> Play 2.0 - Scala app detected` for Play 2.0.x Scala apps. Play 2.0.x language detection is based on file count and is for informational purposes only.

    :::term
    $ git push heroku master
    -----> Play! app detected

### Build behavior

#### Play 1.2.x

During the build phase of your application the following commands are run:

    :::term
    play dependencies --forProd --forceCopy --silent
    play precompile --silent

#### Play 2.x

Play 2.x uses SBT to build your application behind the scenes. On Heroku SBT will be called directly to build your app:

    :::term
    sbt clean compile stage

The local ivy repo is cached between builds to improve performance.

### Environment

The following environment variables will be set at first push:

#### Play 1.2.x

* `PATH`: .play:.tools:/usr/local/bin:/usr/bin:/bin
* `JAVA_OPTS`: -Xmx384m -Xss512k -XX:+UseCompressedOops
* `PLAY_OPTS`: --%prod -Dprecompiled=true 
* `PORT`: HTTP port to which the web process should bind
* `DATABASE_URL`: URL of the database connection

#### Play 2.x

* `PATH`: .sbt_home/bin:/usr/local/bin:/usr/bin:/bin
* `JAVA_OPTS`: -Xmx384m -Xss512k -XX:+UseCompressedOops
* `SBT_OPTS`: -Xmx384m -Xss512k -XX:+UseCompressedOops
* `REPO`: /app/.sbt_home/.ivy2/cache
* `DATABASE_URL`: URL of the database connection

### Runtime behavior

Heroku currently uses OpenJDK to run your application. OpenJDK versions 6 and 7 are available using a `system.properties` file. OpenJDK 8 with Lambdas is also available as an early preview. See the [Java Tutorials](/categories/java) for more information.

#### Play 1.2.x

The default `web` process definition is:

    web: play run --http.port=\$PORT \$PLAY_OPTS

#### Play 2.0.x-2.1.x

A start script is generated by the [xbst-start-script-plugin](http://github.com/typesafehub/xsbt-start-script-plugin). This start script is used by the default `web` process definition:

    web: target/start -Dhttp.port=$PORT $JAVA_OPTS

#### Play 2.2+

The default web process is determined by examining output from the [native packager plugin](http://www.scala-sbt.org/sbt-native-packager/universal.html). If your app only contains one process, the following default web process is generated:

    web: target/universal/stage/bin/{your project name} -Dhttp.port=$PORT

Note: `JAVA_OPTS` is read by the generated script and does not need to be passed into the process.

### Supported versions

#### Play 1.2.x

* 1\.2\.3
* 1\.2\.4
* 1\.2\.5rc3
* 1\.2\.5
* 1\.2\.6

#### Play 2.x

Play 2.x relies on sbt dependency resolution so all versions are available as soon as they are released.

### Add-ons

A Postgres database is automatically provisioned for Play framework applications. This populates the DATABASE_URL environment variable.