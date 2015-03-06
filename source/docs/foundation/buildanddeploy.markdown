---
layout: page
title: "Build and Deploy Environment"
date: 2014-05-24 16:07
comments: true
sharing: true
footer: true
toc: true

---

## Maven 

### Parent POM Configuration

The project `clb-appPlatform` consists of the Parent POM which will build the overall Clouburo App Platform via Maven [Project Aggregation](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Project_Aggregation)

#### Deploy Management

The release and deploy management to Github/Bitbucket is managed by the [wagon-git](http://synergian.github.io/wagon-git/) Maven plugin.

There are two kind of repository locations *Public* (for Open Source Code) and *Private* (Not release in public yet).

Each location consists of three repository

* clb-mvnsnapshot
* clb-mvnrelease
* clb-mvnsite

The `distributionManagement` element in the POM will define the target for the `deploy` and `site-deploy` commands. 

	<distributionManagement>
		<repository>
			<id>clb-mvnrelease</id>
			<name>Cloudburo Bitbucket Maven Release</name>
			<url>git:releases://git@.../clb-mvnrelease.git</url>
		</repository>
		<snapshotRepository>
			<id>clb-mvnsnapshot</id>
			<name>Cloudburo Bitbucket Maven Snapshot</name>
			<url>git:snapshots://git@.../clb-mvnsnapshot.git</url>
		</snapshotRepository>
		<site>
			<id>clb-mvnsite</id>
			<url>git:site://git@.../clb-mvnsite.git</url>
		</site>
	</distributionManagement>

#### Referencing Repositories for Consuming

Having public, as well as private repositories to reference for consuming there are 4 entries in the `repositories` element of the POM

	<repositories>
		<repository>
			<id>clb-mvnrepo-snapshot</id>
			<name>Cloudburo Public Maven Snapshot Repo on Github</name>
			<releases><enabled>false</enabled></releases>
		  <snapshots><enabled>true</enabled></snapshots>
			<url>https://raw.github.com/talfco/clb_mvnrepo/raw/snapshots</url>
		</repository>
		<repository>
			<id>clb-mvnrepo-release</id>
			<name>Cloudburo Public Maven Release Repo on Github</name>
			<releases><enabled>true</enabled></releases>
			<snapshots><enabled>false</enabled></snapshots>
			<url>https://raw.github.com/talfco/clb_mvnrepo/raw/releases</url>
		</repository>
		<repository>
			<id>clb-mvnrepo-private-snapshot</id>
			<name>Cloudburo Private Maven Snapshot</name>
			...
		</repository>
		<repository>
			<id>clb-mvnrepo-private-release</id>
			<name>Cloudburo Private Maven Release</name>
			...
		</repository>
	</repositories>

### Remarks

In case you are running in deploy issues, follow these steps:

wagon-git attempts to reuse as much as possible the same temp directory as for a given remote git repository. It could be possible that this represents a problem. It could be a good idea to perform a test deploy through the command:

`mvn clean deploy site-deploy -DperformRelease -Dwagon.git.safe.checkout=true`

