---
layout: page
title: "clb-appEngineBackend"
date: 2013-11-17 13:24
comments: true
sharing: true
footer: true
toc: true

---

## Java REST Backend

This is a utility library for a Google AppEngine Java REST backend. It's fully managed by Maven.

The API specification which is implemented can be found here [http://cloudburo.github.com/docs/opensource/jsonapispec/](http://cloudburo.github.com/docs/opensource/jsonapispec)

## Source Code Location

Fork me on GitHub: [https://github.com/talfco/clb-appEngineBackend](https://github.com/talfco/clb-appEngineBackend)

## Maven Repository

Add the following entry to your POM:

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

And the following dependency

	<dependency>
		<groupId>com.cloudburo</groupId>
		  <artifactId>clb-appEngineBackend</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>  


## Implementation Aspects

### GSON Converters

The Cloudburo library uses [Gson](https://code.google.com/p/google-gson/) as a  library to convert Java Objects into their JSON representation. It can also be used to convert a JSON string to an equivalent Java object. Gson can work with arbitrary Java objects including pre-existing objects that you do not have source-code of. It's a open source library (Apache License 2.0).

Within the package `con.cloudburo.servlet` there is the `GsonWrapper` class which provides specific serializer- and deserializer for the following conversions

* DateTime Type 
* Date Type
* LocalDateTime Type
* Objectify Key Type

Special care has to be taken for the Objectify Key Type, which cannot be deserialized by the default GSON implementation. There are various questions posted to this topic in the Google Groups Support forum, as well as Stack Overflow, as one comment stated

  > Private fields, immutable objects, and odd constructors are all common problems in the world of deserializers.
  
I've chosen the following approach to deserialized  Objectify Key attributes via the GSON library.

The following sample shows a complex Objectify class which will got persisted. It also contains a `Key` attribute referencing an entity `Service`.

```java
    @Entity
    public class ProductType {
      public @Id Long _id;
      public String name = "";
      public String category = "";
      public String description = "Test this out";
    	
      public ProductType.Relations relations;
      @Embed
      static class Relations {
      	  @Embed
      	  static class ProductTypeServiceConfig {
      	      public @Id Long _id;
      	      public enum Periodicity {  M, W, V, D}; 	
      	      public Periodicity periodicity;
      	      public Integer count;
      	    	public Key<Service> service;
      	  }
      	  public List<ProductTypeServiceConfig> productTypeServiceConfigs;
        }
    } 
```

In order to deserialize this object the following strategy was chosen. 

### Objectify key with 2 constructor parameters

* The creation of a key element is dependent on a `Long id` or a `String name` as well as a kind, e.g `Key<Car> rootKey = Key.create(Car.class, 959);`
* In the above example a Objectify a service identifier would look like something like '[Service("5066549580791808")]', a combination of type and identifier.

The serialization step will now only return the identifier or name to the consumer, to be inline with any other identifier type used in the protocol.

```java
	   public JsonElement serialize(Key key, Type type, JsonSerializationContext serialContext) {
	     logger.log(Level.INFO,"Serialize "+key);
	     if (key.getId() == 0)
	       return new JsonPrimitive(key.getName());
	     else
	       return new JsonPrimitive(key.getId());
	   }
```

The deserialization step has now to take into consideration the type information as well in order to create a correkt Key instance, by inspecting the type input parameter. 

```java
    public Key deserialize(JsonElement element, Type type,  JsonDeserializationContext deserialContext) throws JsonParseException {
      // When we have a Parent Key dependency we have to pass in the parent key - tbd
		  StringTokenizer tok = new StringTokenizer(element.getAsString(),";");
	    String className = ((ParameterizedType)type).getActualTypeArguments()[0].toString(); 
	    if (className.startsWith("class ")) { className = className.substring("class ".length()); }
	    logger.log(Level.INFO,"Deserizalize Objectify key:"+element.getAsString()+" "+className);
	    try {
	       Class clazz = Class.forName(className.toString());
		 	  if (tok.countTokens() > 0) 
		 	  {
			     String parentKey = tok.nextToken();
			     // TODO We have to include here the parent !!!
			     return Key.create(clazz,Long.parseLong(element.getAsString()));
		 	  }
		 	  else {
			     return Key.create(clazz,Long.parseLong(element.getAsString())); 
		 	  }
      } catch (Exception e) {
	    	 throw new JsonParseException("ObjectifKey conversion failed "+e.getMessage());
	    }
	  }		 
```



### Objectify key with 3 constructor parameters (Parent Key)

Will be provided in a later build

## Sample

A simple example code can be found in the directory `src/test/java/com/cloudburo/entity'

### Entity Class

Simply define for each Entity to be persisted an Objectify annotated based class . Make sure to have each attribute defined as `public`

```java 
	package com.cloudburo.entity;
	
	import com.googlecode.objectify.annotation.Entity;
	import com.googlecode.objectify.annotation.Id;
	import com.googlecode.objectify.annotation.Index;
	import java.util.Date;
	import org.joda.time.LocalDateTime;
	
	@Entity
	public class Customer {
		@Id public Long _id;
		@Index public String name;
		@Index public String surname;
		@Index public String email;
		public String address;
		public String plz;
		public String location;
		public Date date;
		public LocalDateTime date1;
		public String telephone;
		public String birthdate;
		public String citizenship;
		public String mobile;
	}
```

### OfyService Class

As a second class extend the  `OfyService` class and register your class.

```java
	package com.cloudburo.entity;
	import com.googlecode.objectify.Objectify;
	import com.googlecode.objectify.ObjectifyService;
	import com.cloudburo.servlet.OfyService;
	
	public class MyOfyService extends OfyService {
		static {
			ObjectifyService.factory().register(Customer.class);
    	}	
		/** Mandatory to override the method **/
    	public static Objectify ofy() { return ObjectifyService.ofy(); }

	}
```

### Servlet Class

Finally provide a Servlet class which extends the abstract `RestAPIServlet` class and you are done.

```java
	package com.cloudburo.entity;
	import com.cloudburo.entity.Customer;
	import com.cloudburo.entity.MyOfyService;
	import com.cloudburo.servlet.RestAPIServlet;
	import com.googlecode.objectify.Objectify;
	
	public class CustomerServlet extends RestAPIServlet {

		protected Class getPersistencyClass() {
			return Customer.class;
		}
	
		protected   Objectify ofy() {
			return MyOfyService.ofy();
		}
	}
```

### Misc

* A `mockito` based Junit based test servlet can be found under `src/test/java/com/cloudburo/servlet`
* An AppEngine Template using the class can be found under Github [https://github.com/talfco/clb-appEngineTemplate](https://github.com/talfco/clb-appEngineTemplate).


