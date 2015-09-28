

# System requirements #
PhpRpcGen has been installed and tested with the following configuration:
  * GWT 2.0.4
  * PHP 5.3.1

Your GWT module must imports the following libraries
```
<inherits name='com.google.gwt.http.HTTP' />
<inherits name='com.google.gwt.json.JSON' />
```

The PhpRpcGen generator is distributed as a set of JAR files: phpgen.jar + the libraries it depends on (see [Download](Download.md) section and the [Developer guide](DevGuide.md)).

# Configuration file #
## Two main sections ##
The configuration file is divided into two main sections:
  * the `<transfer_objects>` element which defines the data structures used as parameters for the remote methods
  * the `<remote_services>` element which defines the remote method interfaces (which should make use of the previously defined "transfer objects" as parameter and result.

```
<?xml version="1.0" encoding="UTF-8"?>
<config>
	<transfer_objects package="com.pyc.client.to">
...
	</transfer_objects>
	<remote_services class="com.pyc.client.MyRemoteService">
...
	</remote_services>
</config>
```

## transfert\_objects section ##
The main element has a "package" parameter:
```
<transfer_objects package="com.pyc.client.to">
```
All the transfer objects defined by one XML configuration file are generated in the same Java package.
### Object specifications ###
Within the "transfer\_objects" element you can find one or more `<object>` elements:
```
<object name="Person">
	<attribute name="firstName" type="String"/>
	<attribute name="lastName" type="String"/>
	<attribute name="fullName" type="String"/>
	<attribute name="age" type="int"/>
	<attribute name="size" type="double"/>
	<attribute name="skills" type="String[]"/>
	<attribute name="childrenAges" type="int[]"/>
	<attribute name="hobby" type="Hobby"/>
	<attribute name="contacts" type="Contact[]"/>
	<attribute name="smart" type="boolean"/>
</object>
```

An object has a name (must be capitalized) to be used as a class name in the generated Java and PHP code.
An oject has multiple "attributes".

### Supported attribute types ###
Here are the types which are supported for object attributes in the XML configuration file. The table also contains the corresponding GWT-Java types.

| **XML configuration type** | **Java type** |
|:---------------------------|:--------------|
| String                     | String        |
| boolean                    | boolean       |
| int                        | int           |
| double                     | double        |
| String`[]`                 | com.google.gwt.core.client.JsArrayString |
| boolean`[]`                | com.google.gwt.core.client.JsArrayBoolean |
| int`[]`                    | com.google.gwt.core.client.JsArrayInteger |
| double`[]`                 | com.google.gwt.core.client.JsArrayNumber |

An attribute can also have an "Object" type. In such case, the type must be specified in a `<object>` element of the same XML file.
Example: the "hobby" attribute of the "Person" object (see above in this page):
```
<attribute name="hobby" type="Hobby"/>
```
should be defined in the same XML file as
```
<object name="Hobby">
	<attribute name="description" type="String"/>
</object>
```

It is even possible to define attributes with an Object array type.
Example: the "contact" attribute of the "Person" object (see above in this page):
```
<attribute name="contacts" type="Contact[]"/>
```

**WARNING**
The "Contact[.md](.md)" will be generated, in GWT Java, as com.google.gwt.core.client.JsArray`<Contact>`.

### A complete example ###
#### The XML configuration structure ####
```
<object name="Person">
	<attribute name="firstName" type="String"/>
	<attribute name="lastName" type="String"/>
	<attribute name="fullName" type="String"/>
	<attribute name="age" type="int"/>
	<attribute name="size" type="double"/>
	<attribute name="skills" type="String[]"/>
	<attribute name="childrenAges" type="int[]"/>
	<attribute name="hobby" type="Hobby"/>
	<attribute name="contacts" type="Contact[]"/>
	<attribute name="smart" type="boolean"/>
</object>
```
#### The generated GWT java code ####
```
package com.test.client.to;

public class Person extends com.google.gwt.core.client.JavaScriptObject {
	protected Person() {
	}

	public static native Person create() /*-{
		return {};
	}-*/;

	public final native String getFirstName() /*-{
		return this.firstName;
	}-*/;

	public final native void setFirstName(String p_firstName) /*-{
		this.firstName=p_firstName;
	}-*/;

	public final native String getLastName() /*-{
		return this.lastName;
	}-*/;

	public final native void setLastName(String p_lastName) /*-{
		this.lastName=p_lastName;
	}-*/;

	public final native String getFullName() /*-{
		return this.fullName;
	}-*/;

	public final native void setFullName(String p_fullName) /*-{
		this.fullName=p_fullName;
	}-*/;

	public final native int getAge() /*-{
		return this.age;
	}-*/;

	public final native void setAge(int p_age) /*-{
		this.age=p_age;
	}-*/;

	public final native double getSize() /*-{
		return this.size;
	}-*/;

	public final native void setSize(double p_size) /*-{
		this.size=p_size;
	}-*/;

	public final native com.google.gwt.core.client.JsArrayString getSkills() /*-{
		if(this.skills==null){
		this.skills=new Array();
		}
				return this.skills;
	}-*/;

	public final native void setSkills(
			com.google.gwt.core.client.JsArrayString p_skills) /*-{
		this.skills=p_skills;
	}-*/;

	public final native com.google.gwt.core.client.JsArrayInteger getChildrenAges() /*-{
		if(this.childrenAges==null){
		this.childrenAges=new Array();
		}
				return this.childrenAges;
	}-*/;

	public final native void setChildrenAges(
			com.google.gwt.core.client.JsArrayInteger p_childrenAges) /*-{
		this.childrenAges=p_childrenAges;
	}-*/;

	public final native com.test.client.to.Hobby getHobby() /*-{
		return this.hobby;
	}-*/;

	public final native void setHobby(com.test.client.to.Hobby p_hobby) /*-{
		this.hobby=p_hobby;
	}-*/;

	public final native com.google.gwt.core.client.JsArray<com.test.client.to.Contact> getContacts() /*-{
		if(this.contacts==null){
		this.contacts=new Array();
		}
				return this.contacts;
	}-*/;

	public final native void setContacts(
			com.google.gwt.core.client.JsArray<com.test.client.to.Contact> p_contacts) /*-{
		this.contacts=p_contacts;
	}-*/;

	public final native boolean getSmart() /*-{
		if(this.smart==null){
		this.smart=false;
		}
		return this.smart;
	}-*/;

	public final native void setSmart(boolean p_smart) /*-{
		this.smart=p_smart;
	}-*/;

}
```
#### The generated PHP code ####
```
<?php
include_once 'contact.php';
include_once 'hobby.php';

//Transfer object class
class Person{
	private static $OBJECTS_METADATA=array(
			'hobby','Hobby',
			'contacts','Contact[]',
		);
	public function getMETADATA() {
		return self::$OBJECTS_METADATA;
	}
	
	public $firstName;
	public $lastName;
	public $fullName;
	public $age;
	public $size;
	public $skills;
	public $childrenAges;
	public $hobby;
	public $contacts;
	public $smart;
}
?>
```

The $OBJECTS\_METADATA class variable is required for the serialization/unserialization activities.

## The remote\_services section ##
This section of the XML configuration file is used to define the interface of the remote service.
Example:
```
<service method="modifyPerson">
	<params type="Person"/>
	<result type="Person"/>
</service>
```
The XML above specifies a method named "modifyPerson" with an input parameter as a Person instance and a result parameter also as a Person.

### Supported types ###
A remote method might have no input parameter but **must** have a result parameter.

---

**WARNING: only object types are allowed as input and result parameters for remote service**
If you want to have a simple type parameter for a remote service you must define some "ValueHolder" transfer object.
Example:
```
<object name="StringValueHolder">
	<attribute name="stringValue" type="String"/>
</object>
```

---


### A complete example ###
#### The XML configuration structure ####
```
<service method="getPersons">
	<result type="Person[]"/>
</service>
```
#### The generated GWT Java code ####
```
public abstract class MyRemoteServiceClient extends JSONRPCRootClient{
		public static final void getPersons(String p_url,AsyncCallback<JavaScriptObject> p_callBack) {
		callJSONRPCService("getPersons",p_url, p_callBack);
	}
...
}
```
#### The generated PHP code ####
```
<?php
include_once 'jsonrpcphp/jsonRPCServer.php';
include_once 'jsonrpcphp/rpc_server_wrapper.php';
include_once 'person.php';
//!!!-----Custom code------

//!!!----------------------

jsonRPCServer::handle(new RPCServerWrapper(null,new GetPersonsServer()));

//RPC service implementation
class GetPersonsServer{
		public function getPersons(){
//!!!-----Custom code------
...
//!!!----------------------
	}
}
?>
```
# Coding #
The remote method code is to be inserted in the PHP server code.
Some editable sections are identified by some special comments:
```
public function getPersons(){
//!!!-----Custom code------
...
//!!!----------------------
}
```

The custome source code inserted between these comments will not be overwritten (if the "keepServerCode" parameter of the ANT task is set to "true").
# The ANT task #
```
<project name="PhpRPCGenerator" default="main" basedir=".">
  <taskdef name="rpcgen" classname="com.pycloux.phpgen.PhpRpcGenTask"/>
  <target name="main">
    <rpcgen
    	configFilePath="config.xml"
    	javaSourcePath="../src"
    	phpSourcePath="../war"
    	keepServerCode="true"
    />
  </target>
</project>
```

You can probably easily understand the meaning of the ANT task configuration parameters:
  * configFilePath: relative path to the XML configuration file
  * javaSourcePath: relative path to the java source code directory
  * phpSourcePath: relative path to the PHP source code directory
  * keepServerCode: if true, the generator will take care not to overwrite the implementation code of the PHP remote service