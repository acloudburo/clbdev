---
layout: page
title: "Automatic Testing"
date: 2012-10-15 22:01
comments: true
sharing: true
footer: true
toc: true

---

## Introduction
**Automated Testing** is a key element in a micro IT company with scarce resources.

* Regression testing of the Clouduro App Platform is fully automated and based on the execellent [SeleniumHQ](http://seleniumhq.org/) Web appliaction testing system.
* Test data is stored in csv file and loaded into the emptied database before a test run is executed. The file handling is delegated to the [org.supercsv](http://supercsv.sourceforge.net/) library, which handles the loading of the csv file rows and converts them into our POJO business domain objects (`customer`,`user` etc.) which then are loaded into the database-

## MongoDB based POJO on the business domain object level

For each business domain object which needs test data a 'Plain Old Java Object? (POJO) must be defined. The definition of a business domain POJO is straightforward and and requires the following steps 

* Define getter- and setter methods for each of its object attributes. The method implementation will delegate the storing and retrieval of the values via the `get`and `put`method which are provided by underlying `com.mongodb.BasicDBObject` parent class.
* `getCSVCellProcessor`method which will return the cell processor required when extracting the data out of a CSV file.
* `getDBCollectionName`method which will return the collection/table name into wich the business domain POJO will be stored in the DB.

The code sample looks like follows:

```java
	public class CustomerDBObject extends DomainDBObject  {
	
	// The CSV Cell Processor
	private static CellProcessor[] csvCellProcessor = new CellProcessor[] {
		new StrMinMax(1,40),  // Expect at least a name with 1-40 characters
		new StrMinMax(1,40),  // Expect at least a surname with 1-40 characters
		null,
		null,
		null,
		null,
		null,
		null,
		null,
		new ParseDate("dd.MM.yyyy")
	};
	
	public  CellProcessor[] getCSVCellProcessor() { 		return csvCellProcessor;
	}
	
	public String getDBCollectionName() {
		return "customers";
	}
	
	public void setName(String name) {
		this.put("name", name);
	}
	public String getName() {
		return (String)this.get("name");
	}
	public void setSurname(String surname) {
		this.put("surname", surname);
	}
```

The `DomainDBObject`is an abstract class which is inherited from the `com.mongodb.BasicDBObject` and which will ensure that the busienss domain POJO's  will provide a `org.supercsv.cellprocessor.ift.CellProcessor`which is required when the test data is loaded from CSV files, as well as the DB collection-/table name.

```java
	public abstract class DomainDBObject extends BasicDBObject {
		public CellProcessor[] csvCellProcessor;
		public abstract CellProcessor[]  getCSVCellProcessor();
		public abstract String getDBCollectionName();
		public DomainDBObject() {
			this.csvCellProcessor = getCSVCellProcessor();
		}
	}
```
	
    
## CSV test data files and generic loader
Test data files are defined per business domain POJO type (e.g. `com.cloudburo.test.dataobj.CustomerDBObject`) and test set (e.g. `fullregression`), as for example

* `com.cloudburo.test.dataobj.CustomerDBObject-fullregression.csv`
* `com.cloudburo.test.dataobj.CustomerDBObject-smallcheck.csv`

The files will be loaded when the corresponding test case is executed (during the test case setup phase)

The CSV data loader part is a generic method, which will make sure that the data are loaded in our  Mongo based POJO domain objects, which can be persisted in the DB in a subsequent processin step.

```java
    public List<DBObject> loadTestDataSet(String set, DomainDBObject dboClass) throws Exception {
    	List<DBObject> list = new ArrayList<DBObject>();
    	ICsvBeanReader inFile = new CsvBeanReader(new FileReader("testdata/"+dboClass.getClass().getName()+"-" +set+".csv"), CsvPreference.EXCEL_NORTH_EUROPE_PREFERENCE);
    	try {
  	      final String[] header = inFile.getCSVHeader(true);
  	      DBObject dbo;
  	      while( (dbo = inFile.read(dboClass.getClass(), header, dboClass.getCSVCellProcessor())) != null) {
  	        list.add(dbo);
  	      }
  	    } finally {
  	      inFile.close();
  	    }
    	return list;
    } 
```

The CsvBeanReader will extract each row and create the corresponding DomainDBObject (in this example the `CustomerDBObject`) and passes in the cell data to the attributes as defined in the header of the file.

	name;surname;address;plz;location;telephone;mobile;email;citizenship;birthdate
	Küstahler;Felix;Zumikerstrasse;8700;Küsnacht;449123567;796249158;talfco@gmx.com;Swiss;17.05.1965
	Meierhans;Albert;Bergstrasse 5;8706;Meilen;449123056;796229108;test@ggaweb.ch;USA;22.08.1996


## Licence overview
* org.supercsv is licenced as [Apache, Licence Version 2.0](http://www.apache.org/licenses/LICENSE-2.0)
* SeleniumHQ is licenced as [Apache, Licence Version 2.0](http://www.apache.org/licenses/LICENSE-2.0)

