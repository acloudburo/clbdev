---
layout: page
title: "The Cloudburo Design Model"
date: 2012-10-20 00:38
comments: true
sharing: true
footer: true
toc: true

---

## Introduction

The heart of the software is a UML Design Model, with a primary focus on the domain and domain logic. It introduces business objects - dervied from textual business concept description - as well as other design work products centered around the model. The model itself is interconnected with the code by serving as a base to generate a subset of the configuration- and software artifacts which gets deployed into the cloud infrastructure. 

## Domain Driven Design

We use a [Domain Driven Design (DDD)](http://en.wikipedia.org/wiki/Domain-driven_design) approach to shape out the business foundation of the  platform.  DDD is is an approach to the design of software, based on the two premises:

* that complex domain designs should be based on a model, and
* that for most software projects, the primary focus should be on the domain and domain logic (as opposed to the particular technology used to implement the system).

Refer to the following [article](http://www.codeproject.com/Articles/56767/Domain-Driven-Design) for a good introduction

  > In other words the heart of the DDD is Model and you start developing your application with drawing your model. Model and  design you create should shape each other. Model should represent knowledge to the business and it is language your team   peak.
  > To be able effectively design you should build your Model and implementation together in the same time, cultivating a language based on the Model. You should represent knowledge in your Model and continuously distill it. You could do refactoring to it doing brainstorming and experimenting.


Integral part of the domain model are the following building blocks:

* _Associations:_ Expresses a relationship between Entities or Value Objects. It's shows that they could be either linked or combined into some aggregation. 
* _Entities:_ Objects that have a distinct identity that runs through time and different representations. You also hear these called "reference objects". Entities are usually big things like Customer, Service Agreement. 
* _Value Object_: Objects that matter only has the combination of their attributes. Two value objects with the same values for all their attributes are considered equal. Values are usually little things like Date, Money, Country.
* _Service_: A standalone operation within the context of your domain. A _Service Object_ collects one or more services into an object. Typically you will have only one instance of each service object type within your execution context.
* An _Aggregate_ is a group of associated objects which are considered as one unit with regard to data changes. The Aggregate is demarcated by a boundary which separates the objects inside from those outside. Each Aggregate has one Root. The Root is an Entity, and it is the only object accessible from outside.

## UML Design Model 

The Domain Model is a UML based model which is an analysis as well as a design model and is a direct connection to the underlying code.   

The UML model is the heart of the software it provides a common language and will not only be used as an aid in early analysis. It's the foundation of the design and ultimately serve as an input to code generators.

This connection between modeling - not just only for analysis work - but also for used for the design (call it model driven design) is a key requirement in a domain driven design.  

Having said that this implies that there is a tight connection between model and the code. As Eric Evans has described in his [book](http://amzn.to/1ijF5xC), which Martin Fowler describes as the canonical source to DDD.

> If the design, or some central part of it, does not map to the domain model, that model is of little value, and the correctness of the software is suspect. At the same time, complex mappings between models and design functions are difficult to understand and, in practice, impossible to maintain as the design changes. A deadly divide opens between analysis and design so that insight gained in each of those activities does not feed into the other.

To establish a model which unifies the analysis as well as the design part(Model-driven design) requires a strong toolset, which is fortunately available as Eclipse Open Source Projects.

We are using the _Eclipse Modelling_ Set, which includes the [Papyrus UML Modeller](http://www.papyrusuml.org/) complemented by [Acceleo Code Generator](http://www.eclipse.org/acceleo/) and includes EMF, UML, OCL features which are required to describe the model in its necessary details. Refer to the [Foundation Chapter](/docs/foundation/index.html) for more details about the toolset.

The model itself is expressed by a set of diagrams, which are means of communication and explanation. It's important to understand that they show design constraints and show conceptually important parts of the object model. But they are not design specifications in every detail.

A lot of the constraints which are driving the code generators are expressed via UML stereotypes attached to UML elements, which are defined in a dedicated UML profile.

As for example in the following diagram attribute _name_ of the business object _Customer_ has some stereotypes attached (representd by _<<...>>_ markers). 

![Business Object with Stereotypes](/images/Voila_Capture356.png)

Going to the detail view will show the textual definition and configuration of the dedicated items.

![Stereotype Definitions](/images/Voila_Capture355.png)  

As an example

* the _Loc_ stereotype is defining the localization of the attribute name when shown on the screen for various languages (de, en, fr ...)
* the _List_ stereotype is used to define if a certain attribute is used in list view of the Business Object and its positioning. In this example the attribute is part of the list view element shown on the _TitleLine_ 


## Business Object Model Diagram

The Business Object Diagram shows objects of importance to a business and documents the relationships between them in terms of responsibilities and behavior. Its emphasizes the roles performed in the business area and their active responsibilities. 

![Business Object Model](/images/Voila_Capture354.png)

### Code Generation Aspects

#### Attribute Handling

In general the generator differentiates the following attribute types

##### Primitive

Will  result in simple attributes in the generated artifacts (javascript backbone models/views, templates, java objective classes etc.).

##### Data Type "referenceData" (Value Object)

Data Type attributes with the Stereotype `referenceData` will be based on name/value list which are fetched by a client from static JSON Files. In the DDD language they are called _Value Object_

As an example the following `country` list

![Country JSON](/images/Voila_Capture359.png)

is the source of data, modelled in the following data type:

![ReferenceData DataType]( /images/Voila_Capture360.png)

The stereotype `referenceData` has a property `refCode` which will provide the information how to fetch the data for the specific ReferenceData DataType,

e.g. `http://<servername>/refdata/<refCode>.<language>.json` 
 
The `language` parameter is set by end user session (Localization). 

Such a data type will be visualized based on a [Select2 Remote Select Box](http://ivaynberg.github.io/select2/), the whole code fragment can be generated.

( /images/Voila_Capture361.png  [ReferenceData DataType] %}
 
##### Enumeration

Enumeration will be mapped to choices and will be visualized as select values.

#####  ToOne Association (Identity Object)

In case we are referencing via a class attribute exactly one other Class Object (which has an identity and a lifecycle, called in DDD an Identity Object) we can generate a simple attribute in the code artefacts.

[One To One Object Associations2] (/images/Voila_Capture358.png)

##### ToMany Association

These class attributes are reflecting an endpoint of a navigable association. It's navigable due to the fact that member end of the association is owned by the classifier (aka the class).

![One To Many Associations](/images/Voila_Capture357.png) 

* E.g. the association member end `serviceConfig` is owned by the classifier `ProductType` and therefore represented as class attribute in the UML class. A product type may include one to many serviceConfig(s) and therefore will be handled specially in the generator.

#### Modeling Persistency Functionality 

The Business Objects will be stored in a [NoSQL](https://en.wikipedia.org/wiki/NoSQL) datastore, which is suited for data that is modeled documents  other that the tabluar relations used in relational databases.

As you can see in the above model diagrams, a class has either the stereotype `documentCollection` or `documentProperty`.

The following holds true:

* A class of `documentCollection` will be persisted as JSON document to the underlying data store.
* A class of `documentProperty` will be persisted as part of the `documentCollection` which is associated in a navigable way with this one ), actually the association is of type aggregation.
* This will allow to buid complex json data structures which are persisted in a transaction. 
* Depending on the Assocation either a single Document Property object will be persisted or a list of Document Property list will be persisted.


As an example the `ProductType-ProductTypeServiceConfig` model construct will result in the following JSON data structure

[Document Structure](/images/Voila_Capture362.png)

Again all the necessary meta information required in the code will be generated, by default each of the Class will have by default a unique identifier `_id`.  

As an example the below screenshot shows you the generated AppEngine [Objectify](https://code.google.com/p/objectify-appengine/) Entity (Backend Java Class). It's class structuring represents the above JSON structure. Such a Entity class object can be transformed from-/to JSON by simply using the Google [GSON](https://code.google.com/p/google-gson/) library. 

![Objectify Entity Class](/images/Voila_Capture363.png)

#### Modeling Visualization Functionality





