---
layout: page
title: "Node.js Server"
date: 2012-10-15 21:46
comments: true
sharing: true
footer: true
toc: true

---


The Node.js based HTTP server is one of the runtime container used by the Cloudburo deployment strategy. [Node.js](http://http://nodejs.org/") is a platform built on [Chrome's](http://code.google.com/p/v8/) JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

## NPM Node Modules
Beside the node.js core server application a set of NPM packaged libraries are used, part of them are also used within the frontend based application (as for example 'underscore' or 'coffee script')

### Clouburo provided Modules

* [clb-modelloader](https://github.com/talfco/clb-modelloader)  is a small utility which is loading moongoose model definition files from a file directory location into a nodejs server instance and automatically creates corresponding request handlers

### Third party provided Modules

* [underscore](http://documentcloud.github.com/underscore/) is a utility-belt library for JavaScript that provides a lot of the functional programming support that you would expect in Prototype.js (or Ruby), but without extending any of the built-in JavaScript objects. It's the tie to go along with jQuery's tux, and Backbone.js's suspenders. Available on [github](https://github.com/documentcloud/underscore/).
* [coffee-script](http://coffeescript.org/): The CoffeeScript compiler is itself written in CoffeeScript, using the Jison parser generator. The command-line version of coffee is available as a Node.js utility. Available on [github](https://github.com/jashkenas/coffee-script).
* [mongodb](http://www.mongodb.org/): MongoDB (from "humongous") is a scalable, high-performance, open source NoSQL database. Available on [github](https://github.com/mongodb/mongo).
* [moongoose](http://mongoosejs.com/): An elegant mongodb object modeling for node.js.
* [express](http://expressjs.com/): Express is a minimal and flexible node.js web application framework, providing a robust set of features for building single and multi-page, and hybrid web applications. Available on [github](https://github.com/visionmedia/express).
* [stitch](https://github.com/sstephenson/stitch): Develop and test your JavaScript applications as CommonJS modules in Node.js. Then Stitch them together to run in the browser. Available on [github](https://github.com/sstephenson/stitch)
* [winston](https://github.com/flatiron/winston#installation): A multi-transport async logging library for node.js. Available on [github](https://github.com/flatiron/winston#installation).
* [uglify-js](https://github.com/mishoo/UglifyJS) JavaScript parser / mangler / compressor / beautifier library for NodeJS. Available on [github](https://github.com/mishoo/UglifyJS)


## File Directory Structure
The file directory consists of two parts

* backend-, server related files are stored in the following directory 
   *  `root` directory itself contains of
   		* the `index.js` file which will initialize the `Node.js` server application
   		* the `cloudburoApp.js` file which bundles the CommonJS modules which makes up the application in a minified single javascript file. 
   		* the `modelLoader.coffee` file which loads the server side moongoose model of the application and injects the necessary event handlers.
   * `root/node_modules` contains all `Node.js` modules required by the server itsself, as for example `express, mongoose, mongodb, stitch, backbone, underscore` etc. These modules are managed via the [Node Package Modules](https://npmjs.org/) mechanism.
   * `server_models`directory contains all Mongoose based business concept object models of the Cloudburo App platform, as for example the models necessary to persists objects like `Customer`, `Service`, `Product`, `User`and so on.   
* frontend-, browser related files are stored in `root/app` directory

### index.js
The `index.js` contains the code for the Node.js configuration it consists of the following main blocks

1. Creating the Stitch package of the client side java script application, which will be transferred to the client during the initial web page opening
2. Setting up the `Node.js` Server and connecting to the cloud based MongoDB instance.
3. Loading all backend business domain model objects - which are based on mongoose - of the cloudburo app platform and bridge them to the corresponding Backbone frontend business domain model objects


## Model Loader
### Generic loading mechanism for backend business domain model
The model loader will dynamically auto load the server models which are stored in the `server_models` directory.
 
 
 ```coffeescript
modelLoader = new (require("./modelLoader"))();
modelLoader.autoload(nodeserv,db, path.join(__dirname,"server_models")); 
```
	
The server models are defining the mongoose schema for the business domain model objects they are responsible for. 

```coffeescript
module.exports = class ServiceTemplateModel extends BaseModel
	constructor: (props) ->
  		super  'serviceTemplateModel','ServiceTemplateModel',
    			new mongoose.Schema
      			name:  { type: String, default: '' }
      			type: { type: Number, default: '' }
      			category:  { type: String}
      			prize:  { type: String}
      			duration:  { type: String}
	newInstance: (props) ->
  		return new ServiceTemplateModel props
```
 

Each server model extends the `BaseModel`

```coffeescript
module.exports = class BaseModel
	constructor: (@modelName,@dbModelName,@schema,props) ->
  		@dbModel = mongoose.model(@dbModelName, @schema)
  		@model = new @dbModel(props)
  		@schema.virtual('id').get -> return this._id

  		@schema.methods.toBackbone = ->
    			obj = this.toObject();
    			obj.id = obj._id;
    			return obj;
	
	getModelName: -> @modelName
	getDBModel: ->  @dbModel 
	getDBSchema: -> @schema
	save: -> @model.save()
```

 
The model loader will load each model module dynamically out of `server_models` directory, creates the relevant moongoose object and passes it over to the BackBoneBridge (see below the expose operation)

```coffeescript
files = fs.readdirSync modelpath

modelNames = _und.map files, (f) => 
  path.basename f
 
bridge = new (require("./backboneBridge"))()
      
_und.each modelNames, (modelName) ->     
  modelC = require modelpath + "/" + modelName 
  util.inspect(modelC,true,true)
  if modelName != "baseModel.coffee"
     model = new modelC()        
     @expose(model,serv)
```

### Generic approach to bridge frontend Backbone business domain models 

The `expose` function will take care that backend requests triggered by  frontend Backbone based business object domain models are dispatched to the corresponding Mongoose based business object domain model which will handle the request in interaction with the database. 
For each server model a set of request handler will be created. In case the server model name is `customerModel.coffee` the URL path name will be the file name (without Model.coffee extension) plus an 's' for the plural, e.g `customers`. The following request handlers are created for each server model:

1. get a list of a business domain objects (`/customers`), GET request
2. update a business domain object (`/customers/<id>`), PUT request
3. delete a business domain object (`/customers/<id>`), DELETE request
4. create a new business domain object (`/customers/<id>`), POST request

The expose function looks like the following

```coffeescript
	expose : (model, serv)->
   		collection = model.getModelName()+'s'

	    serv.get '/'+collection, (req, res) ->
     		   model.getDBModel().find({}, (err, docs) -> 
             if err != null
               res.json err, 500
            else
         	  res.send(docs))

	   serv.put '/'+collection+'/:id', (req, res) ->
	     conditions  = { _id: req.params.id }
	     doc = req.body
	     delete doc._id
	     model.getDBModel().update conditions, doc,{}, (err, numAffected) -> 
	       if err == null 
	         res.json  200
	       else
	         res.json err, 500
	
	   serv.del '/'+collection+'/:id', (req, res) ->
	     conditions  = { _id: req.params.id }
	     model.getDBModel().remove conditions, (err, numAffected) -> 
	     	if err == null 
	         res.json  200
	       else
	         res.json err, 500    
	
	   serv.post '/'+collection, (req, res) ->
	     conditions  = { _id: req.params.id }
	     doc = req.body
	     obj = model.newInstance doc 
	     obj.save()  
	     res.send  obj.model
````
