---
layout: page
title: "clb-modelloader"
date: 2012-12-23 13:17
comments: true
sharing: true
footer: true
toc: true

---

## Overview

The Clouburo Modelloader  `clb-modelloader` is a small utility which is loading moongoose model definition files from a file directory location into a nodejs server instance and automatically creates corresponding REST request handlers for handling the *C*reate, *R*ead, *U*pdate and *D*elete (*CRUD*) operations.

The request handlers are expecting a JSON document and will take over the persistency interaction part with the moongoose library.

The library can be easily integrated with a client side Backbone model which results in a transparent handling of the DB interactions for CRUD operations in a Backbone backed Web Application.

Because REST is an architectural style and not a strict standard, it allows for a lot of flexibility. The library will make sure that each model will offer an interface which is following the same API design principles.

The REST API style offered is following the pragmatic (not dogmatic) REST API design guidelines as outlined in the free [eBooks](http://apigee.com/about/api-best-practices) by apigee. 

## Source Location

Fork me on GitHub: [https://github.com/talfco/clb-modelloader](https://github.com/talfco/clb-modelloader)

## Getting Started

###  NPM

The library is as NPM module available, you have to run:

    npm install clb-modelloader
  
###  Moongoose Business Object Models

Put all your moongoose business object model (= collection) into a directory from where they will be loaded into the nodejs application server. The model load will accept either coffee files ending in `.coffee` or direct java script files endin in `.js`. A server model file will look like the following:
	
```coffeescript 
	BaseModel = require "clb-modelloader/BaseModel"
	
	module.exports = class CustomerModel extends BaseModel
	
	constructor: (props) -> 
      super 'Customer','customers', 
        surname : { type: String, default: '' }
        name : { type: String, default: '' }
        email : { type: String, default: '' }
        address : { type: String, default: '' }
        plz : { type: String, default: '' }
        location : { type: String, default: '' }
        telephone : { type: String, default: '' }
        mobile : { type: String, default: '' }
        birthdate : { type: Date, default: Date.now }
        citizenship : { type: String, default: '' }
        props
    
    newInstance: (props) ->
      return new CustomerModel props
```   

You are passing to the super constructor the model name, collection name, as well as the schema attributes. In our sample above this will result in a mongodb collection of the name `customers`.

You would store this model in under the file name `customerModel.coffee` and place it for example in the directory `server_models`.

#### Naming Conventions
The following naming conventions should be followed:

* A model file should start with a small letter and must end in with the postfix _Model_
	* E.g. _customerModel.coffee_, _productTypeModel.js_ etc.
* A model class name must start with a capital letter and the rest as the fileName
	* E.g. _CustomerModel_, _ProductTypeModel_ 	  

### Node.js Server Integration

Within the Node.js server code part you will add something like that into the 'index.js' 

```javascript 
     ModelLoader = require("clb-modelloader/ModelLoader");
     // Winston will handle the logging
     winston = require("winston");
     
     // … Do the app initialization ...
    
    // Load Application Modules pass in
    // - MongoDB connection string
    // - The version number of your API
    // - A winston log object
    // - A URL where you would provide API error descriptions
    modelLoader = new ModelLoader("mongodb://user:password@myinstance.mongolab.com:29897/mydomain","v1",winston,"http//:api.cloudburo.com/errors/");
    // Load all .coffee or .js files in the server_models directory 
    modelLoader.autoload(nodeserv,path.join(__dirname,"server_models"));
```
    
 
## Exposed JSON API

Refer to the [Cloudburo JSON API Specification](/docs/opensource/jsonapispec) for details about the data API specification.

## Frontend Backbone Integration

### Backbone Business Object Models

Suppose that your are using the [Backbone Framework]() on your client side (browser), so you have to provide a corresponding Backbone model to the above defined Moongoose Business Object Model.

#### Backbone Model File

```coffeescript 
    Backbone = require "vendor/backbone"
    module.exports = class Customer  extends Backbone.Model
    
    idAttribute: "_id" 
    
    defaults: ->
      surname: ""
      name: ""
      email: ""
      address: ""
      plz: ""
      location: ""
      telephone: ""
      mobile: ""
      birthdate: null
      citizenship: ""
```
    
That's the whole Backbone model for our `customer`. The corresponding collection is even simpler. 

#### Backbone Collection File

```coffeescript 
    Backbone = require "vendor/backbone"
    Customer = require "./customer"
	
    module.exports = class Customers extends Backbone.Collection
      model: Customer 
      url:'v1/customers'
```
    
Mission completed, moongoose model and corresponding backbone files defined.

### Backbone View Integration

As soon as the user will do a GUI action (e.g. saving a modified record entry, deleting one or creating a new one) you will have to manipulate the underlying Backbone model. In this example there would be a `saveToModel` function to persists changes in a record. That would look like

```coffeescript 
    window.Backbone =  require "vendor/backbone"
    
    module.exports = class RootView extends Backbone.View
    # Some code …
    
    getCurrentId: ->
      if @model.has "_id"
        @model.get "_id" 
      else 
        return "new" 
    
    # Suppose that saveToModel method is called when a user presses a save button
    saveToModel: -> 
      # This will part will be called in case a new record is added
      if @getCurrentId() == "new"
        @collection.add @model, silent:true
        @collection.trigger("reset", {})
        
      @model.save({}, 
        success: (collection, response) => 
          # Some code here, the transfer to the backend moongoose was done transparently 
```

In the delete case the code would look like

```coffeescript 
    deleteRecord: (id) =>
      @close()
      @model.destroy 
        success: (model, response) =>
        # Show some completion dialog
```

So the data traffic triggered by the Backbone model manipulation will be consumed by our moongoose server side models and executed against the mongodb persistency layer. 

This glue code is implemented in the `BaseModel` class

```coffeescript 
    mongoose = require "mongoose"
    
    module.exports = class BaseModel
    
    constructor: (@modelName,@collection,@schema, props) ->
      @dbModel = mongoose.model(@modelName, @schema,@collection)
      @model = new @dbModel(props)
      @schema.virtual('id').get -> return this._id

    @schema.methods.toBackbone = ->
      obj = this.toObject();
      obj.id = obj._id;
      return obj;
```