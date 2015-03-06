---
layout: page
title: "Google AppEngine"
date: 2012-10-15 21:46
comments: true
sharing: true
footer: true
toc: true

---

## Introduction

[Google App Engine](https://developers.google.com/appengine/docs/whatisgoogleappengine) is a Platform as a Service (PaaS) offering that lets you build and run applications on Google’s infrastructure. Google App Engine supports apps written in a variety of programming languages, Cloudburo is using the Java implementation.

After exeperimenting with various Cloud offerings (Heroku, Cloudflare, Amazon AWS) I decided to use the Google App Engine as the primary platform for the Cloudburo App offering.

Google App Engine makes it easy to build and deploy an application that runs reliably even under heavy load and with large amounts of data. It includes the following features:

* Persistent storage with queries, sorting, and transactions.
* Automatic scaling and load balancing.
* Asynchronous task queues for performing work outside the scope of a request.
* Scheduled tasks for triggering events at specified times or regular intervals.
* Integration with other Google cloud services and APIs.

Applications run in a secure, sandboxed environment, allowing App Engine to distribute requests across multiple servers, and scaling servers to meet traffic demands. Your application runs within its own secure, reliable environment that is independent of the hardware, operating system, or physical location of the server. 

Three major features of the Google App Engine, which are important for a platform supporting micro enterprises, are

* App Engine applications can authenticate users using any one of three methods: Google Accounts, accounts on your own Google Apps domains, or OpenID identifiers.  An application can detect whether the current user has signed in, and can redirect the user to the appropriate sign-in page to sign in or, if your app uses Google Accounts authentication, create a new account. The app can also detect whether the current user is an administrator, making it easy to implement admin-only areas of the app. I think to leverage the Google Account capability the cumbersome and rather complex authentication process can be leveraged. 
* The Integration with other Google cloud services, which allows to leverage capabilities provided  for example by applications like Google Sheet, Google Word etc. This can greatly reduce implementation efforts in the area of exporting data to Excel spreadsheets, producing printable invoice forms etc. 
* Google App Engine comes with a [full blown SDK](https://developers.google.com/appengine/downloads) for Java, which allows to emulate all of the App Engine services on your local computer. 

## Application Layering

### Google Services

As described above the infrastructure foundation layer is the Google App Engine PaaS, with the following services

* [App Engine Datastore](https://developers.google.com/appengine/docs/java/storage#app_engine_datastore):  A schemaless object datastore with automatic caching, a sophisticated query engine, and atomic transactions
* [Capabilities](https://developers.google.com/appengine/docs/java/capabilities/): With the Capabilities API, your application can detect outages and scheduled downtime for specific API capabilities
* [Logs](https://developers.google.com/appengine/docs/java/logs/): The Logs API provides access to the application and request logs for your application. 
* [Users](https://developers.google.com/appengine/docs/java/users/): App Engine applications can authenticate users using any one of three methods: Google Accounts, accounts on your own Google Apps domains, or OpenID identifiers

### Google Objectify

[Ojectify](https://code.google.com/p/objectify-appengine/) is a Java data access API specifically designed for the Google App Engine datastore. It occupies a "middle ground"; easier to use and more transparent than JDO or JPA, but significantly more convenient than the Low-Level API. Objectify is designed to make novices immediately productive yet also expose the full power of the GAE datastore. It's a open source library (MIT License).

The Cloudburo code generator will generate entity object based on the Objectify library and will integrate to the REST API interface via the google GSON library.

### Google GSON

[Gson](https://code.google.com/p/google-gson/) is a Java library that can be used to convert Java Objects into their JSON representation. It can also be used to convert a JSON string to an equivalent Java object. Gson can work with arbitrary Java objects including pre-existing objects that you do not have source-code of. It's a open source library (Apache License 2.0)

### Cloudburo AppEngine Backend Library

This is our provided utility library for a Google App Engine Java REST backend. The API specification which is implemented can be found here

[http://cloudburo.github.com/docs/opensource/jsonapispec/](http://cloudburo.github.com/docs/opensource/jsonapispec)

More Documentation can be found in Open Source section of the documentation:
[http://cloudburo.github.com/docs/opensource/clb-appenginebackend/](http://cloudburo.github.com/docs/opensource/clb-appenginebackend)


## Remarks

### Google AppEngine and UTF-8

The Cloudburo App is using the [UTF-8](http://en.wikipedia.org/wiki/UTF-8) characters which can represent every character in the Unicode character set. Server developers as well as me were confronted with the problem that the local test AppEngine instance is supporting by default UTF-8 but the cloud base production environment may not by default.

To solve that problem took some hours so it's worth to provide some details about the issue and its solving. There are two aspects which must be considered

* Forcing the client to submit form data as utf-8: A browser submit data in the same encoding used to render you page. So the index page hosting the Single Page Application includes the following meta tag: `<head><meta charset="utf-8"></head>`.
* Forcing the server to interpret submitted data as utf-8..  Browser's don't submit content type encoding with their POSTs. This means the server has to "guess" it, and java web containers just automatically default to the platform default file.encoding.Almost always latin-1. 

The solution is that you must call `request.setCharacterEncoding("utf-8")` *before* you call request.getParameter().  The easiest way is with a servlet filter that
executes early. Now there are posts which claim that this is not working with appengine. Therefore the AppEngin Backend Library `RestAPIServlet` will take care of that, be ensuring input request and output response are correctly setting the character set. Something like:

```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
      throws IOException, ServletException {
      req.setCharacterEncoding("UTF-8");
      resp.setCharacterEncoding("UTF-8");
      ...
```