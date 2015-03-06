---
layout: page
title: "Cloudburo Concepts"
date: 2014-05-04 10:03
comments: true
sharing: true
footer: true
toc: true

---

## Introduction

Business Concepts are used to standardize the terms and definitions of the data objects and attributes stored in the Cloudburo IT Platform .
It's a full and factual representation of the business reality grounded in business concepts relevant for the platform and based on a textual description.

In the area of DDD it is known also as _Ubiqitous Language_

> The vocabulary of the _Ubiquitous Language_ includes the names of classes and prominent operations. The _Language_ includes terms to discuss rules that have been made explicit in the model. It is supplemented with terms from high-level organizing principles imposed on the model (such as _Context Maps_ and large-scale structures,). Finally, this language is enriched with the names of patterns the team commonly applies to the domain model.

## Core Concepts

* A micro **company**  with a service oriented value proposition consists of an **owner** and potentially few additional **employees** each of them providing dedicated skills and **capabilities** which are required by the company to earn money and generate profit
* The company is offering a dedicated set of **services** to its customers which are based on the overall capabilities assembled by its company staff.
* The delivery and consumption of the service by the customer requires the participation of staff employee(s) for a certain amount of time at a given date (e.g. teaching a lesson), so-called  **service delivery sessions**
* In order to sell these services to its **customers** the company will bundle dedicated **services** into **products**  which are purchasable by the customer.
* A customer who bought the product is entitled to consume the offered services and will become a (**product consumer**)
* A product may include additional non-human service related **goods items** which are complementing or supporting the service offering (e.g. a text book in case of a educational service offering)
*  An employee will have a weekly or monthly **availability** (full, part-time) or maybe hired for a service execution on request. Additionally an employee may **absent** for holiday etc. during the year.
* The company will manage its product offering and customer relationship via its IT driven infrastructure **operation platform** which aims to reduce the administrational effort of the company and helps to increase and improve the product sales process.

> A company will offer a set of products each of it will bundle dedicated service items of the company (which are based on the capabilities of its employees). If necessary the product will be complemented by additional physical or electronical good items (e.g. text book) which are complementing the product.

## Customer Profiling Concepts

* A customer is a person which has at least one product ordered (product owner) from the company
* A non-customer person may be a product consumer or referenced person in a product as associated consuming thing. They are **potential prospects** for the company.
* A person may be in a **personal relationship** (spouse, wife, child) with another person. 
* An associated consuming thing may be referenced and associated in multiple product orders of potential different product owners.
* An associated consuming thing may have a **thing relationship** with a product owner or consumer ( e.g. for a dog there is a legal dog owner defined, which could be a product consumer)

> For example: Marie Meyer (product owner/consumer) will order the dog education course "PD11" together with her dog Bebbo (associated consuming thing). Her husband Frank Meyer (personal relationship to Marie as husband) wants also to participate with Bebbo in the same course and will order the PD11 with the same dog. For the dog, the legal registered dog owner (thing relationship) is Marie, which is an important fact which must be printed onto the course certification statement.


## Product Commercialisation Concepts

The  _Core Concepts_ are the foundation of the three core operating processes, which are driving - according to John Hagel -  the company performance.

* customer relationship management, 
* product innovation and commercialization 
* infrastructure operations. 

### Product Template
A product is based on a **product template**, which defines in a generic way the content of the product and its service provisioning features. So the template will contain the collection of services which are included in the product (as well as potentially additional goods). A service has a **service description**, as well as a  **service duration** (e.g. course lesson of one hour). Within a product template a **service occurence** can be one to multiple time. In case a service will occur multiple time within a product template, a **service occurence periodicity** may be defined
  
> For example the company "Universal Language School" has the capability to provide the service of teaching lessons for the English language. One of its courses is offering advanced English language education and is offered to the customers as "English Language Course Advanced", which consists of eight lessons of 90 minutes.  
So the product template for this course would include the service "English Teaching Lessons", with a duration of 90 minutes and an occurence of eight. Furthermore the product would include an additional good item, the English grammar book.

Furthermore a service may be provided by a employee with the necessary **service capability know how** and may have a **default service price.**

> In our example A "English teaching lesson" can be provided by any employee with the capability of "English teacher" and may cost 80.-.

### Product Instantiation
Out of a product template a **product instance** can be created, which is purchasable by a customer.
The product instantiation includes the following step:

1. A unique **product instance identifier** must be allocated
1. For each listed service and each service occurence, a **service session date and time** must be defined. If in the product template a service occurence frequency was defined (e.g. weekly), the system will propose subsequent service session dates and times after the first date/time was set.
1. Each service occurence instance will get a unique **service session instance id** 

### Product Offering and Ordering
The product instance is visible to the customer, which can study the **product sales descriptions**, what kind of services are included, as well as if there are any additional goods delivered. The description includes any **terms and conditions**, as well as possible **sales restrictions** on a product or service level.  
A customer can buy the product via a **product order.**  The bought product will belong to the **product owner** and its included services are linked/associated with the product consumer (which could be another person as the product owner). Beside the product consumer the ordered product may include an  optional **associated consuming thing** which will play an important part of the product service delivery .

> For example in dog teaching classes, a puppy course is provided for the dog owner (product consumer) together with his dog (associated consuming thing). Or a baby swim course is provided for the mother together with her baby.

As a convention a a product order may have **one** product consumer and optional **one** associated consuming thing. 

###  Summary Product Feature Types
A product may include the following feature types

* Static Features: Which are set when a product template is defined
* Dynamic Features: Which are set when a product is instantiated from the template
* Linkage Features: Which are set when a product is ordered and linked to product consumers and associated consuming things.

# Core Metrics Concepts

For a micro company with its limited and constrained resource power, it’s mandatory  that the staff can focus on activities which will increase their customer base and market penetration. From the above business types the **customer relationship managment** backed by an efficient  **product commercialization process** of its capabilities will be key.  
Normally product  innovation doesn’t really play a significant role in a micro company (well there are few people which have some dedicated capabilities which they want to commercialize), as well as infrastructure operations and the hassle of maintaning and running it should be outsourced. 

The following key metrics are relevant in order to track the performance of the micro company in the above key areas

* Customer relationship management is all about connecting with a set of customers, getting to know them deeply and delivering more and more value to them.  The metric that drives the performance of this core operating process is simple: **customer life-time value**. Customer life-time value itself is a function of three variables:  
*[(Profit generated per year) x (years of relationship)] – cost of customer acquisition*

* Similar to customer relationship management, the metric that drives the Product commericalization core process is simple: **product life-time value**.  This metric can also be decomposed into three variables:  
*[(Profit generated per year) x (years of market life)] – cost of developing the product*


