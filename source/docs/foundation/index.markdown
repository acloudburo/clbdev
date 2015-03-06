---
layout: page
title: "Platform foundation"
date: 2012-12-10 23:07
comments: true
sharing: true
footer: true
toc: true

---

## Introduction

The Foundation Chapter will describe the foundation of the Cloudburo App Platform. It will introduce the major elements (principles, concepts, frameworks, libraries, tools ...) of the platform in order to bootstrap a cloud based service delivery business which includes

* architectual principles and concepts
* frameworks and libraries
* development tools
* as well as development workflow and processes 

It's the nucleus for building up the platform.

## Architecture

### Principles

The architecture of the Cloudburo Platform is heavily influenced by [Reacive Manifesto](http://www.reactivemanifesto.org/), which are nicely summarized by the following 

> New requirements demand new technologies. Previous solutions have emphasized managed servers and containers. Scaling was achieved through buying larger servers and concurrent processing via multi-threading. Additional servers were added through complex, inefficient and expensive proprietary solutions.

> But now a new architecture has evolved to let developers conceptualize and build applications that satisfy today’s demands. We call these Reactive Applications. This architecture allows developers to build systems that are event-driven, scalable, resilient and responsive: delivering highly responsive user experiences with a real-time feel, backed by a scalable and resilient application stack, ready to be deployed on multicore and cloud computing architectures. The Reactive Manifesto describes these critical traits which are needed for going reactive.

Complemented by the principles of:  

* *Data on the Wire*. Don't send HTML over the network. Send data and let the client decide how to render it.
* *Embrace the Ecosystem*. Integrates, rather than replaces, existing open source tools and frameworks. Release part of the work as open source.
* *Simplicity Equals Productivity*. The best way to make something seem simple is to have it actually be simple. Reduce to the Essential.
* *Use Metadata and Generate*. Structure and standardize the code, use meta data as source for code generators instead of reimplementing and copying code.


### Concepts

#### Domain Driven Design (DDD)

We use a [Domain Driven Design (DDD)](http://en.wikipedia.org/wiki/Domain-driven_design) approach to shape out the technical foundation of the  platform.  DDD is is an approach to the design of software, based on the two premises:

* that complex domain designs should be based on a model, and
* that for most software projects, the primary focus should be on the domain and domain logic (as opposed to the particular technology used to implement the system).

This is described in the [Business Model](/docs/model) chapter.

#### Application Structuring  (SPA)

A Cloudburo Platform application is a mix of JavaScript that runs inside a client web browser, as well as JavaScript that runs on the nodejs server inside a Node.js container, which is serving all the supporting HTML fragments, CSS rules, and static assets.

The application is based on the Single Page Application design principles, which seperates the functional layer as an API and the presentation layer in form of a HTML5 App. Details about this approach and further links can be found in my blog entry [Single Page Application Driven by Javascript](http://cloudburo.github.com/blog/2012/12/21/single-page-application-driven-by-javascript/)

JavaScript is the main asset on client as well as on the server. Concerning the server asset, the Cloudburo App Platform will gather all the Javascript files used by client into one Application file which will be executed within the browser.

#### Templates

HTML GUI Elements (View Panes, Forms, Menu Tabs etc.) are served by the nodjs server as *underscore* templates, which will attached to the to HTML DOM element and cached during a user session. After arriving of new data in the browser, the relevant tempate will be fetched, compiled with the data set and rendered to the end-user. 

#### Code Generation
The Cloudburo code generator facility is based on the [Papyrus UML Modeller](http://www.papyrusuml.org/) in combination with the [Acceleo Code Generator](http://www.eclipse.org/acceleo/).

* Papyrus will persist its model repository in the  EMF-based implementation of the UML 2.2 metadmodel for the Eclipes Platform ([API Reference](http://help.eclipse.org/indigo/topic/org.eclipse.uml2.doc/references/javadoc/overview-summary.html)).
* Acceleo Code Generator will use a MOF Model to Text Transformation Language based code generator [OMG MOFM2T](http://www.omg.org/spec/MOFM2T/1.0/) specificiation which is capable of interpreting the EMF-base UML model. 
* Integral part  of Acceleo as well is a Object Constraint Language [OMG OCL](http://www.omg.org/spec/OCL/) compliant query language interpreter for selection the relevant model elements.

Rather a powerful toolset which is provided in a fully open source version. 


## Programming Languages
A twin programming language approach consisting of `Javascript` and `Java` was chosen.

### JavaScript / CoffeeScript

Main development language for the Enduser Application (Frontend and Backend) is `JavaScript`. I prefer to use `CoffeeScript` which is a little language that compiles down to JavaScript. The syntax is inspired by Ruby and Python and implements many features from those two languages.

> Underneath all those awkward braces and semicolons, JavaScript has always had a gorgeous object model at its heart. [CoffeeScript](http://coffeescript.org/) is an attempt to expose the good parts of JavaScript in a simple way. 

CoffeeScript is very succinct, and takes white space into consideration, this reduces code by third to a half of the original pure JavaScript and increases readability. In addition CoffeeScript has some neat features, such as array comprehensions, prototype aliases, and classes. 

The compiler converts CoffeeScript code into its counterpart JavaScript, there's no interpretation at runtime.

### Java

Having a Java developer background going back to version 1.0 which was released in 1995, it was clear for me to use Java as a second programming language for writing code generators and any kind of utility programs required for the platform.
Java provides a lot of powerful libraries for any kind of utility tasks and it's strong object-oriented language backed by an excellent IDE's (e.g. Eclipse) is an ideal complementing piece to JavaScript, as the main development language.

## Development Environments (IDE)

A cornerstone for a robust and fast development process is a state of the art integrated development environment which bundles and coordinates the various activities.

Currently I'm using two flavours of IDE's

* Eclipse IDE for Java- or UML-driven development work
* Textmate which is a lightweigther and more agile editor centric development environment for any other work.

### Eclipse IDE

Eclipse IDE is by far one of the most adavanced IDE for any kind of development actitivities. It provides a vast array of plugins for any kind of software lifecycle activities. Especially it provides strong support for a Model Driven Development approach which is one of the principle of the Cloudburo development work. Currenlty I use the following Eclipes setup for my work:

* Eclipse Luna  [_IDE for Java EE Developers_](http://www.eclipse.org/downloads/)
	* [Mylyn](http://www.eclipse.org/mylyn/downloads/)  is complemented by [BitBucket Integration Plugin](http://www.mylynbitbucketconnector.xpg.com.br/update) which allows to sync Tasks with my remote Bitbucket Repository	
* [Eclipse Luna Core Extension Plugins](http://download.eclipse.org/releases/luna)
	* _Modeling_ Set, which includes [Papyrus UML Modeller](http://www.papyrusuml.org/), [Acceleo Code Generator](http://www.eclipse.org/acceleo/), EMF, UML, OCL features which I require for the model driven code generation part. 
* Google AppEngine (not on Luna available yet)
  * The Google Developer plugins, therefore use the Eclipse Kepler plugin still 
* [Aptana Plugin](http://download.aptana.com/studio3/plugin/install) for Web Development Editors etc. (e.g. Coffee Script)
* Database Plugins
	* 	[MonjaDB Feature]( http://www.jumperz.net/update/) is a MongoDB GUI client tool for rapid application development. It aims to provide a thoroughly straightforward way of updating MongoDB documents.
	* 	[Toad for Cloud Databases Plugin](http://toaddownload.quest.com/toadforcloud/eclipse/releases)
* Build Plugins
	* [Maven Integration Plugin](http://download.eclipse.org/technology/m2e/releases) 
* Testing Plugins
	* [TestNG Plugin](http://beust.com/eclipse), I use TestNG for the test automation of GUI based test cases.	

A drawback of Eclipse is its quite resource hungry and can consume considerable memory space. Therefore I introduced Textmate as my main development editor, which is far more agile and integrates very nicely in various programming languages and tools.

### Textmate

[Textmate](http://manual.macromates.com/en/preface#philosophy_of_textmate) as advertised as the _the missing editor for the Mac_ is a very powerful Editor with a huge bundle repository which allows to integrate almost any development language or activity. The philosophy of textmate is following the one of Unix tasks.

* First it has good shell integration, so if you are skilled in using the UNIX shell, you should love TextMate.
* But more ambitiously, TextMate tries to find the underlying patterns behind automating the tedious, being smart about what to do and then to provide you with the functionality so that you can combine it for your particular needs.

I use it as a main IDE for doing javascript, coffee-script, HTML, CSS3, template, scripting work. 

## Source Code Management
As a version control system I use the de facto standard for open source application [GIT](http://git-scm.com/), which is complemented by [Bitbucket](https://bitbucket.org)  which is a web-based hosting service for projects that use either the Mercurial or Git revision control systems. This allows me to manage a remote copy of my code which I don't make (yet) available via [my GIT](https://github.com/talfco) open source account.


## Build and Deployment Environment
Overall build and deploy is managed by Maven. Details can be found [here](buildanddeploy.html)

## Utitilities

### Direct installed OSX Applications
* [Node.js](http://nodejs.org/) Platfrom. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

### Ruby Gems

* [Jekyll](http://jekyllrb.com/) Transforms plain text into static websites and blogs. Prerequisite for Octopress Blogging-/Documentation platform

### Homebrew (Package Manager OSX)
[Homebrew](http://brew.sh/) is the so-called missing package manager for OS X. Homebrew complements OS X and is based on git and ruby.


### NPM (Node Package Manager)
[NPM](https://www.npmjs.org/doc/) is the node.js based package manager and is required for my node deployment platform as well as a package manager for certain utitilities, e.g `Bower`.


### Bower

As Package Manager for keeping my frontend and node deployment platform under control, I use the [Bower](https://www.npmjs.org/package/bower) package manager.

Bower is a package manager for the web. It offers a generic, unopinionated solution to the problem of front-end package management, while exposing the package dependency model via an API that can be consumed by a more opinionated build stack. There are no system wide dependencies, no dependencies are shared between different apps, and the dependency tree is flat.

* GIT
* Ruby

### Brunch

I replaced my earlier [Codekit](http://incident57.com/codekit/index.php) build tool with the awesome [Brunch](http://brunch.io/) ultra-fast HTML5 build tool. It's very handy in order to build my Single Page Application Bundle (javascripts, underscore templates, less compiles, static assets etc) which will be integrated into the Google App Engine

### Dash

Invaluable documentation reference help is [Dash](http://kapeli.com/dash). Dash is an API Documentation Browser and Code Snippet Manager. Dash stores snippets of code and instantly searches offline documentation sets for 130+ APIs. 


### SourceTree

SourceTree is my most preferred GUI for managing my GIT repositories on a Mac. It's provided as open source application by [Atlassian](http://www.sourcetreeapp.com/).

## Open Source Libraries

### Javascript Browser Libraries

#### Underscore
[Underscore](http://documentcloud.github.io/underscore/) is a utility-belt library for JavaScript that provides a lot of the functional programming support that you would expect in Prototype.js (or Ruby), but without extending any of the built-in JavaScript objects. It's the tie to go along with jQuery's tux, and Backbone.js's suspenders.

#### Jquery
[JQuery](http://jquery.com/) is a fast, small, and feature-rich JavaScript library. It makes things like HTML document traversal and manipulation, event handling, animation, and Ajax much simpler with an easy-to-use API that works across a multitude of browsers. 

#### Backbone
This the MVC framework used for Cloudburo Apps. [Backbone.js](http://documentcloud.github.io/backbone/) gives structure to web applications by providing models with key-value binding and custom events, collections with a rich API of enumerable functions, views with declarative event handling, and connects it all to your existing API over a RESTful JSON interface.

#### Bootstrap from Twitter
The most popular front-end [framework](https://github.com/twbs/bootstrap) for developing responsive, mobile first projects on the web.

#### XEditable

This powerful [library](http://vitalets.github.io/x-editable/index.html) allows you to create editable elements on your page. It can be used with any engine (bootstrap, jquery-ui, jquery only) and includes both popup and inline modes.

#### Select2

[Select2](http://ivaynberg.github.io/select2/) is a jQuery based replacement for select boxes. It supports searching, remote data sets, and infinite scrolling of results and is integrated into XEdtiable.

#### Datatables
[DataTables](http://datatables.net/) is a plug-in for the jQuery Javascript library. It is a highly flexible tool, based upon the foundations of progressive enhancement, and will add advanced interaction controls to any HTML table.

#### Moment
A javascript date [library](http://momentjs.com/) for parsing, validating, manipulating, and formatting dates.

### Javasript Server Libraries 
These libraries are used for the Node.js implementation.

#### Express
[Express](http://expressjs.com/) is a minimal and flexible node.js web application framework, providing a robust set of features for building single and multi-page, and hybrid web applications.

### Miscellaneous

#### Reference Data: Country List

List of all countries with names and ISO 3166-1 codes in all languages and all data formats were provided by [link](https://github.com/umpirsky/country-list) by using [CLDR - Unicode Common Locale Data Repository](http://cldr.unicode.org/). ([Licence](https://github.com/umpirsky/country-list/blob/master/LICENSE))



