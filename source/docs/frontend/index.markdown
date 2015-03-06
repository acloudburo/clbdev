---
layout: page
title: "Javascript Frontend Application"
date: 2012-10-15 21:46
comments: true
sharing: true
footer: true
toc: true

---

## Introduction

As outlined above the main functionality of the Cloudburo App Platform will run as a Javascript based application in the browser of the end user. An is using the based on the following javascript frameworks and libraries.

### Terminology and Abbreviation
The following terms and abbreviations will be used 

* Business Domain Object (_BDO_) describes the main entities which are managed within the application, as for example a _customer_, an _employee_, a _course_ etc.  

### Framework and Technologies used

#### Bootstrap
The Cloudburo App Platform is based on the open sourced and incredible powerful front-end framework [Bootstrap from Twitter](http://twitter.github.com/bootstrap/), which handles all the user interface related aspects as for example styles (CSS), user interface components (tool tips, popovers, tabs, alerts, page header), javascript plugins and so on. 

#### Backbone

[Backbone](http://documentcloud.github.com/backbone/) is a great platform for writing client side applications. Backbone.js gives structure to web applications by providing models with key-value binding and custom events, collections with a rich API of enumerable functions, views with declarative event handling, and connects it all to your existing API over a RESTful JSON interface. It's one of the cornerstone client side libraries used in the Cloudburo App Platform.

####  Backbone.ModelBinder
The [Bacbkbone.ModelBinder](https://github.com/theironcook/Backbone.ModelBinder) class is a great utility in order to bind backbone model attributes to:

* Read-only html elements such as `<span>`, `<div>` etc.
* Html element attributes such as enabled, displayed, style etc.
* Editable form elements such as `<input>`, `<textarea>` etc. 
* This type of binding is bidirectional between the html elements and the Model's attributes.

As the creator and github user theironcook described

> The ModelBinder class helped me remove a lot of cluttered boilerplate code that existed to synchronize my models and views. As my application became more asynchronous, the ModelBinder saves me from a lot of pain by automatically displaying model attributes in the view as they are asynchronously loaded. Hopefully you'll find the ModelBinder useful too.

It's very useful and I used it to manage all my input forms in a generic way (more later).

####  Stitch
The [Stitch](https://github.com/sstephenson/stitch) Package Creator, which will bundle all Javascript Libraries in one file `application.js` and creates an `App` class which will be instantiated in the browser window after the HTML is loaded.

Looking in a browser window at the source html of the Cloudburo App Platform you will see only a minimal HTML document below 20 lines of code:  

```html
<html>
<head>
	<meta charset=utf-8>
	<title>Cloudburo App Center</title>
	<link rel="stylesheet" type="text/css" href="/application.css">
	<link rel="stylesheet" type="text/css" href="/bootstrap.css">
		<script src="/application.js" type="text/javascript" charset="utf-8" />
		<script type="text/javascript" charset="utf-8">
		  document.addEventListener("DOMContentLoaded", 
		    function(){ (require("app")).init();}, 
		    false
		  );
		</script>
	</script>
</head>
	<body></body>
</html>
```

So the complete control is handed over to the javascript _App_ application, after this minimal HTML is loaded in the browser window (includes the download of the application javascript code).

## Main Class: App
As described above the `App`class is the main class which instantiates the overall application. 

* It contains a global variable _BusinessObjectDef_ which defines main configuration of *BDOs* available to the end user.
* It will load the main application template (application skeleton) and generates the corresponding HTML content.
* It will attach Backbone Views for the `MainTabMenuView` and the `IconbarView`
* It will instantiate the `AppRouter` (a Backbone Router) and delegates the control to it.


```coffeescript
# Step 1: Retrieve the main page template 
$.get "/views/templates/main.show", (data) => 
    $('body').append(data) 
    tmpl =   document.getElementById('tmpl_mainpage').firstChild
    tmpl1 = _und.template tmpl.textContent
    $("body").append tmpl1
      'home' : 'Home', 
      'home_person' : 'Person', 
      'home_account' : 'Account'
      'home_profile' : 'Profile'
      'search' : 'Search', 
      'help' : 'Help', 
      'help_manual' : 'Online Manual'
      'help_faq' : 'FAQ'
      'help_support' : 'Support Request'    

   
    TabMenuView = require("views/base/tabMenuView")
    tabMenuView = new TabMenuView 
    tabMenuView.render()

    IconbarView = require("views/base/iconbarView")
    iconbarView = new IconbarView 
    iconbarView.render()
    
    AppRouter = require("./appRouter")
    window.appRouter = new AppRouter()
    window.app = this;
    Backbone.history.start();
```


### Main Configuration Structure and Caching
The global variable `window.BusinessObjectDef` defines the overall configuration of the application concerning available _BDO's_ to the end user.

```coffeescript
window.BusinessObjectDef =
    customers :
      mainRoute: "customers"  
      dialogAttr1: "surname" 
      dialogAttr2: "name"
    servicetemplates:
      mainRoute: "serviceTemplates"
      dialogAttr1: "name"
      dialogAttr2: ""
```

In the example above the application will offer the _BDO_ types _customers_ and _serviceTemplates_.

In the final version the assembly of _BDO_ types offered to a customer will be dependent on the subscription level. 

The `App`class will hold a global cache variable ` window.cache = new Array()` which will cache Backbone models or views already opened and used during a user session.


## Event Overview

### Event: Browser URL Change
This event can be either triggered manually by user typing in an URL in the broweser window or by calling the `window.appRouter.navigate`operation, which is an instantiation of the Backbone Router class (holding all routes of the application.)  

#### Pub 1: Browser.urlOpening
Entering a browser URL in the form of

`http://cloudburo.ch/#customers`
	
and hitting return, will trigger the AppRouter dispatcher.

#### Pub 2: BaseMenuView.tabMenuChange

This event will be triggered in case the user wants to work with another _BDO_ type (e.g. working with the domain objects `user`instead of `customer`). That means the detail view, as well as the list view will be switched. 

This event is triggered for example when he clicks on a menu tab (`customer`or `user`) or any other icon associated via a _BDO_ type.

This will trigger a `window.appRouter.navigate` operation (details refer to [link](#anchor1))

#### Pub 3: RootView.deleteRecord
When a record is deleted the view is refreshed by calling
`window.appRouter.navigate @viewNameL , {trigger: true}`

#### Pub 4: RootView.cancel
When a cancel button is pressed and no previous entry is available.

#### Consumer 1: Approuter.routes

Our `AppRouter`class is derived from the `Backbone.Router`and is responsible to dispatch navigation changes which are reflected in the URL.

A navigation change is either happening when the user types a URL into the browser and hits return oder programmatically in our javascript application, e.g. by a menu tab click event (see above).

Refer to the following registered URL:

```javascript
routes:
     ""							: "root" 
     "customers"					: "listCustomer"
     "customers/"          		: "listCustomer"
     "customers/:id"				: "detailCustomer"
     "customers/:id/modify"		: "modifyCustomer"
     "servicetemplates"			: "listServiceTemplate"
     "servicetemplates/"     		: "listServiceTemplate"
     "servicetemplates/:id"		: "detailServiceTemplate"
     "servicetemplates/:id/modify": "modifyServiceTemplate"
```

Now each triggering of an operation will result in an action similiar like
	
```javascript
listCustomer: ->
      log.info "AppRouter.listCustomer"
      @switchBusinessObjectEvent "customers"
      @renderListView("Customer")

  detailCustomer: (id) ->
      @switchBusinessObjectEvent "customers"
      log.info "Approuter.detailCustomer: /customers/id called with id '"+id+"'"
      @renderDetailView("Customer",id,window.viewStateView )
```


The `Approuter.switchBusinessObject`operation will be call by routes operation and do the following step:

*  Will check if the _BDO_ template is already loaded, if not it will load it now (= lazy loading in order to reduce size of initial page build up)
*  Will trigger the `currentBusinessObject` event notifying other components that the _BDO_ were changed and the views replaced.

The code looks like:
	
```javascript
switchBusinessObjectEvent : (busObj) => 
      busObjShort = busObj.substring(0,busObj.length-1)
      if $("#tmpl_"+busObjShort+"_contentsheet").size() == 0
        $.get "/views/templates/"+busObj+".show", (data1) => 
          log.debug "Approuter.switchBusinessObjectEvent: Template loaded"
          $('body').append(data1)
          window.Notifier.trigger(window.Notifier.eventCurrentBusinessObject, busObj)  
      else
        window.Notifier.trigger(window.Notifier.eventCurrentBusinessObject, busObj) 
```
  
 For more details refert to [link](#anchor2)  

### Event: MenuTabEntryClick 

#### Pub 1: BaseMenuView.mouseClick
 
The `BaseMenuView`class (`views/base/baseMenuView.coffee`) installs click listeners which will be triggered in case the user clicks on `<a>`element in the menu or icon bar [more details](#anchor3). 

```coffeescript
events:
'click #cuh': 'switchMenuTab'
 	'click #cuo': 'showView'
 	'click #cum': 'modifyView'
 	'click #cun': 'newView'
 	'click #cud': 'deleteView'
 	'click #cuc': 'copyView' 
 	'click #cup': 'printView'
```

The `BaseMenuView` is a actually the parent class of `TabMenuView` class, as well as the `IconMenuView` class. The icon bar will provide the same click functionality as the tab menu (`cum-cuh`). 

![TabMenuView and IconMenuView](/images/menuAndIconView.png)

#### Sub 1: BaseMenu.<CRUD>View 

The main functionality of the `BaseMenuView` operations are to trigger a global view state event upon which consumers may react.

```coffeescript
showView: -> 
  	log.debug "BaseMenuView.showView"
  	window.Notifier.trigger window.Notifier.eventCurrentViewState, window.viewStateView
  	@postClickProcessing()
  	return false

modifyView: ->
  	log.debug "BaseMenuView.modifyView"
  	window.Notifier.trigger window.Notifier.eventCurrentViewState, window.viewStateModify
  	@postClickProcessing()
  	return false
```

The `postClickProcessing`operation does by default nothing but may overwritten by a inherited class, e.g. the `TabMenuView`class is overwriting this class in order to close the drop down menu again

```coffeescript
postClickProcessing: ->
  	log.debug  "TabMenuView.postClickProcessing" , @busObj
  	$('li[class~="open"]').removeClass 'open'
```

#### Sub 2: [BaseMenu.switchMenuTab](id:anchor1)

A special case is the `switchMenuTab` operation, which handles the event when user clicks on a tab mennu entry (drop down menu is closed)

```coffeescript
switchMenuTab: (event )->
	a = event.target.parentElement.getAttribute("id")
  	bobj = a.substring(0,a.length-3)
  	log.debug 'BaseMenuView.switchMenuTab: "'+bobj+'" current scope on "'+@busObj+'"'
  	if bobj != @busObj
  		@busObj = bobj
  		window.appRouter.navigate("/"+@busObj,{trigger: true})
```

* The operation will identify the _BDO_ selected by querying the dropdown id `<li id="servicetemplates-dp" class="dropdown">` and by getting rid of the last three characters. 
* It will check if the clicked tab is already selected (stored in the class varialble `@busObj`) if not the `AppRouter.navigate` operation will be called.

### Event: ChangeBusinessObjectScope

Even which will triggered in case the Business Object scope will change, i.e. the user changes from the business object 'customer' to the 'order' one. As well as a initial Browser opening with a specific URL.

#### Pub 1: BaseRouter.buildDetailView
See above this event will be triggered as a result of a of renderDetailView request originiated by the `switchMenutTab` or a browser opening with a specific URL. 

#### Sub 1: BaseMenuView.changeBusinessObjectScope
Will consume the event. In case the passed in business object domain type is different the menu tab must be adjusted. 

#### Sub 2: TabMenuView.changeBusinessObjectScope
Extends the `BaseMenuView` will control the toggling of TabMenu entries (on/off)

#### Sub 3: NavbarMenuView.changeBusinessObjectScope
The navigation bar holds the search input field, which has a selection dropdown attached which allows to select the search field. Dependent on the business object domain type shown on the screen (in the example below the `Customer`) the drop down selection values will be adjusted accordingly.

![TabMenuView and IconMenuView](/images/Voila_Capture320.png)

### Event: ChangeBusinessObjectElement
	
Will be triggered in case the detail view model was changed to a new one. This can be either triggered by

1. A `changeBusinessScope` event happened beforehand due to a initial browser window opening or the user changed scope via menu or tab change
2.  The user selected another element in the list view
3.  The user saved a new element in modify view


#### Pub 1: BaseRouter.buildDetailView 

Will handle the constellation 1 and 2 of the above event triggering.

#### Pub 2: RootView.saveToModel

Will handle constellation 3 of the above event triggering. This is method will also triggering the `eventCurrentViewState` 

#### Pub 3: RootListItemView.click

Will be triggered in case the user clicks on a list entry in the List View. The Event Trigger is configurable (`RootListItemView`is used in multiple list view) and passed in during creation of a list item.

#### Sub 1: RootView.changeModel

The operation `changeModel` will be triggered, which will check if the passed in target model has to be replaced or not and takes over kicks off rendering part.

#### Sub 2: RootListView.changeModel

The operation `changeModel` will be triggered which will highlight the entry. the Event Trigger is configurable  and passed in during creation of a list item. Analogue to the publishing even above on `RootListItemView`.

### Event: CurrentViewState
		
#### Pub 1: BaseMenuView.modifClick
If the user selects the modify entry or new entry the event will be triggered

#### Sub 1: rootView.currentViewStateEvent
Depending on the state will close and reopen the view

```javascript
currentViewStateEvent: (viewState) =>
    log.debug "RootView.currentViewStateEvent "+@state+" - "+viewState
    if @state != viewState
      @state = viewState 
      @close()
      @render()
```


### Event: changeContentTab

Will be triggered when the the content tabs in a view are changed (i.e. the so-called pill list)

#### Pub 1: RootView.render

The rendering of the root view 	will trigger the event

#### Sub 1: RootView.changeContentTab

The operation will toggle the content tabs.


## [Class AppRouter](id:anchor2)      

The `AppRouter`class provides a set of functions to manage and rendering the Views of the application, theres are 

* `newInstance`
* `renderViews`
* `renderListView`
* `renderDetailView`

### newInstance (caching of Views and Models)

The newInstance is used for the creation of Views and Model and will make use of caching, i.e. any view/model already opened will be cached during the user sessions.

```coffeescript
newInstance: (strClass) ->
  	# Let's cache the Views
    if window.cache[strClass] != undefined
      return window.cache[strClass]
    else  
      args = Array.prototype.slice.call arguments, 1 
      clsClass = eval strClass
      F = () -> return clsClass.apply this, args
      F.prototype = clsClass.prototype
      window.cache[strClass] = new F()
      return window.cache[strClass]
```

## Views

### Main Dropdown Menu and Iconbar

The main dropdown menu will consist of a list of all _BDO's_ with its associated functions. 

#### Dropdown Templates
The menu part of each _BDO_ is defined as part of the `views/templates/main.show.tpl`and consists of HTML fragments like


```html
<script type="text/template" id="tmpl_customers_dropdownmenu">
  	<li id="customers-dp" class="dropdown"> 
    <a id="cuh" href="#" class="dropdown-toggle" data-toggle="dropdown"><%= customer_menu_header %><b class="caret"></b></a> 
    <ul class="dropdown-menu"> 
      <li><a id="cuo" href="" data-toggle="tab"><%= customer_menu_overview %></a></li>
      <li class="divider"></li>
      <li><a id="cun" href="" data-toggle="tab"><%= customer_menu_new %></a></li> 
      <li class="divider"></li>
      <li><a id="cum" href="" data-toggle="tab"><%= customer_menu_modify %></a></li>
      <li><a id="cud" href="" data-toggle="tab"><%= customer_menu_delete %></a></li>
      <li class="divider"></li>
      <li><a id="cup" href="" data-toggle="tab"></span><%= customer_menu_print %></a></li> 
    </ul> 
  </li>
</script>
	<script type="text/template" id="tmpl_servicetemplates_dropdownmenu">
	<li id="servicetemplates-dp" class="dropdown"> 
    <a id="cuh" href="#" class="dropdown-toggle" data-toggle="dropdown"><%= servicetemplate_menu_header %><b class="caret"></b></a> 
    <ul class="dropdown-menu"> 
      <li><a id="cuo" href="" data-toggle="tab"><%= servicetemplate_menu_overview %></a></li>
      <li class="divider"></li>
      <li><a id="cun" href="" data-toggle="tab"><%= servicetemplate_menu_new %></a></li> 
      <li class="divider"></li>
      <li><a id="cum" href="" data-toggle="tab"><%= servicetemplate_menu_modify %></a></li>
      <li><a id="cud" href="" data-toggle="tab"><%= servicetemplate_menu_delete %></a></li>
      <li class="divider"></li>
      <li><a id="cup" href="" data-toggle="tab"></span><%= servicetemplate_menu_print %></a></li> 
    </ul> 
  </li>
</script>
```
 
#### [Standardized Trigger Points](id:anchor3)
 
Each of these fragment blocks are identifiable and loadable via the identifier `<businessdomainobject>s-dp`, e.g. `<li id="customers-dp" class="dropdown"> p`. There are predefined trigger points defined attached to the `<a>`element in the fragment.

![Menu Trigger Points](/images/menuTriggerpoints.png)
 
 * `cuh`: Is the root trigger point for a tab switch, i.e. the current _BDO_ screens will be replaced through the new one and the drop down menu will be displayed.
 * `cuo`: Open Overview Screen, which shows a read only view of a dedicated _BDO_ (the selected one, by defaul the first one) 
 * `cun`: New _BDO_ Screen
 * `cum`: Modify _BDO_ Screen
 * `cud`: Delete _BDO_ Screen
 * `cup`: Print _BDO_ Screen

### Main View: RootView class

As described above the `BaseMenuView`class will get triggered in case the user wants to change the view state, for example to create, modify or delete a _BDO_. The class will trigger a global event in the form of `window.Notifier.trigger window.Notifier.eventNewRecord` which will be consumed by the `RootView`class in order to do the view state change.

#### Trigger Events and cached Views

It's important to mention that the application will cache view instances during a user session. That means in case the user will trigger a tab menu change the application will check in its cache if the view was already opened beforehand or not. In case the view was already used beforehand the cached instance will be visualized immediately (this handled in the `AppRout.newInstance` method). 

In context of a event trigger based applications some special considerations must be taken. E.g. any view state change trigger event will be consumed by the actual visible view but also all the cached ones which are currently hidden, _BUT_ only the visible view should react on the view state change. That means the view must know if he is the actual visible one or not. 

This boolean information will be derived through  `window.Notifier.eventChangeModel` events which are consumed by `RootView`class as well.


#### Event: eventChangeModel




## Helpful Utilities

Creating a object via reflection

```javascript
newInstance: (strClass) ->
      args = Array.prototype.slice.call arguments, 1 
      clsClass = eval strClass
      F = () -> return clsClass.apply this, args
      F.prototype = clsClass.prototype
      return new F()
```
   
JavaScript lacks a built-in function for getting the exact class names of object instances. This function will retrieve the name via the constructor.
     
```javascript
getObjectClass: (obj) ->
  	if (obj && obj.constructor && obj.constructor.toString) 
    	arr = obj.constructor.toString().match(/function\s*(\w+)/);
    	if (arr && arr.length == 2) 
      	return arr[1];
  	return undefined;
```

