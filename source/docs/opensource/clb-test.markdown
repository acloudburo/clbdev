---
layout: page
title: "clb-test"
date: 2013-11-10 22:37
comments: true
sharing: true
footer: true
toc: true

---

## Overview

The Cloudburo `clb-test` provides a simple framework to load Test Data from a Excel CSV into your persistens storage layer. It's fully managed by Maven

Currently the following persistent storage layers are supported.

* NonSQL Mongo DB
* Any persistent storage which provides are RESTful API, as described in in [http://cloudburo.github.com/docs/opensource/clb-modelloader/](http://cloudburo.github.com/docs/opensource/clb-modelloader)

It based on the following open-source frameworks/libraries

* __testng__: TestNG is a testing framework inspired from JUnit and NUnit but introducing some new functionalities that make it more powerful and easier to use,
* __com.google.gson__: Gson is a Java library that can be used to convert Java Objects into their JSON representation
* __org.joda-time__: Joda-Time provides a quality replacement for the Java date and time classes
* __org.apache.httpcomponents__: A toolset of low level Java components focused on HTTP and associated protocols. 
* __net.sf.supercsv__: Super CSV is to be the foremost, fastest, and most programmer-friendly, free CSV package for Java.

When executing

      mvn test

The Test driver will load the testng xml configuration file from the `testconfig` and loads the `testdata` into the configured persistency backend (either Google App Engine or Mongo DB) by using the Java test data objects defined in `/test/com/cloudburo/test`.

The current sample definitions/testdata will work in combination with the GoogleApp Engine Java Sample Skeleton which is available in [clb-appEngineTemplate](https://github.com/talfco/clb-appEngineTemplate)

The Initial Load Test Driver is integrated into a `TestNG`testscript and can be found under `src/test/java/com/cloudburo/test/InitialLoadTestDriver.java`. 

It can be easily extended to your needs. 

## Source Code Location

Fork me on GitHub: [https://github.com/talfco/clb-test](https://github.com/talfco/clb-test)

## Maven Repository ##

Add the following entries to your POM:

    <repositories>
    	<repository>  
        	<id>clb-mvnrepo-snapshots</id>  
        	<name>Cloudburo Maven Repo Snapshot on Github</name>  
        	<releases><enabled>false</enabled></releases>
            <snapshots><enabled>true</enabled></snapshots>
        	<url>https://raw.github.com/talfco/clb_mvnrepo/raw/snapshots</url>  
    	</repository>
    	 <repository>  
        	<id>clb-mvnrepo-release</id>  
        	<name>Cloudburo Release Repo on Github</name>  
        	<releases><enabled>true</enabled></releases>
            <snapshots><enabled>false</enabled></snapshots>
        	<url>https://raw.github.com/talfco/clb_mvnrepo/raw/release</url>  
    	</repository>
    </repositories>

And the following dependency (adjust the version number if necessary)

	<dependency>
		<groupId>com.cloudburo</groupId>
		  <artifactId>clb-test</artifactId>
		<version>1.0.0</version>
	</dependency>  
