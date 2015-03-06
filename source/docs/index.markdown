---
layout: page
title: "Cloudburo Platform Documentation"
date: 2012-10-14 17:51
comments: false
sharing: true
footer: true

---


# Overview

The Cloudburo App Platform is designed for cloud based web application serving micro businesses. The bootstrapping of this application platform is done during my spare time and allows me to exploit new technologies and frameworks.  
Due to the fact that I can only devote partial time in the design and development of the platform (beside my full time IT job at a Swiss Bank) it's an absolute necessity to document the work rigorously in order to take up development work efficiently after a longer break. 


> My experience is that a lot of tiny design and implementation decisions done during extensive programming sessions (e.g. during late night session) are fading away quite quickly out of the short term-memory and only a fuzzy image is surviving in some distant memory twists (well at least in my brain is acting in this). This results then unproductive sessions where I try to understand what I was assembling together some weeks ago. Anyway a good documentation will help in any case and will solidify the design and development foundation of the platform ;)  

_Be aware this is work in progress and will be evolutionary and incrementally extended._


# Business Vision 

[Go to Chapter](/docs/vision.html) 

# Foundation

[Go to Chapter](/docs/foundation) 

# Business Concepts

[Go to chapter](/docs/concepts/index.html)

# Model Driven Design

[Go to Chapter](/docs/model)

# Technical Backend Design

[Go to Chapter](/docs/backend)

# Technical Frontend Design

[Go to Chapter](/docs/frontend)

# Testing

[Go to Chapter](/docs/testing) 

# Deployment 
* **[Oracle VM Virtual Box](/docs/deployment/virtualbox)** provides an overview how to setup a Oracle VM Virtual Box for the testing out the deployment concept.

# Open Source 

Will contain all work which was released as Open Source


* [Cloudburo JSON API Specification](/docs/opensource/jsonapispec) is the data API documentation of the Cloudburo JSON API. The format and protocol will be used by the various components provided by the Cloudburo App Platform.
* [clb-modelloader](/docs/opensource/clb-modelloader) is a small utility which is loading moongoose model definition files from a file directory location into a nodejs server instance and automatically creates corresponding REST request handlers for handling the *C*reate, *R*ead, *U*pdate and *D*elete (*CRUD*) operations (following the above described API specification).
* [clb-appEngineBackend](/docs/opensource/clb-appenginebackend) This is a utility library for a Google AppEngine Java REST backend. It’s fully managed by Maven.
* [clb-appEngineTemplate](/docs/opensource/clb-appenginetemplate) is a skeleton application for a Google App Engine Java REST backend. It provides sample code for a standardized REST API which is used within Cloudburo and makes used of the `clb-appEngineBackend] utility
* [clb-test](/docs/opensource/clb-test) provides a simple framework to load Test Data from a Excel CSV into your persistens storage layer. It's fully managed by Maven..



# Miscellaneous Chapter

*	**[Managing Content](/docs/contentmgmt.html)** - will give you an overview of the used content management and blogging platforms for bootstraping the static web content of the company.

