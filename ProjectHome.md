A code generator (delivered as a simple ANT task) providing a simple implementation of RPC for GWT (as a client) and PHP (as a back-end).
It makes use of JSON-RPC as the underlying RPC protocol.
The code generator is able to produce both Gwt-Java client code and PHP server stub.



# What is PhpRpcGen #

PhpRpcGen aims at simplifying the integration of GWT with PHP back-ends. The GWT-RPC is really easy to use but requires a Java back-end.
The GWT documentation explains how to integrate GWT client code with a PHP back-end using JSON (see http://code.google.com/webtoolkit/doc/latest/tutorial/JSONphp.html). However the coding environment is not really developer friendly.

PhpRpcGen provides a GWT-PHP-RPC mechanism based upon JSON-RPC as a client/server serialization protocol (the initial version http://json-rpc.org/wiki/specification since it is fairly sufficient).

PhpRpcGen is based upon a kind of IDL (written in XML) that is used to generate both GWT-Java client code and PHP server code. The code generator is delivered as a simple ANT task.

# An example ? #
Here is a very simple example which should allow to understand how PhpRpcGen works.

### The configuration file (the kind of IDL) ###
```
<?xml version="1.0" encoding="UTF-8"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="config.xsd">
	<transfer_objects package="com.pyc.client.to">
		<object name="Person">
			<attribute name="firstName" type="String"/>
			<attribute name="lastName" type="String"/>
			<attribute name="fullName" type="String"/>
	</object>
	</transfer_objects>
	<remote_services class="com.pyc.client.MyRemoteService">
		<service method="getPersons">
			<result type="Person[]"/>
		</service>
	</remote_services>
</config>
```

## The ANT task ##
A simple ant task with only 4 parameters:
```
<project name="PhpRPCGenerator" default="main" basedir=".">
  <taskdef name="rpcgen" classname="com.pycloux.phpgen.PhpRpcGenTask"/>
    <rpcgen
    	configFilePath="config.xml"
    	javaSourcePath="../src"
    	phpSourcePath="../war"
    	keepServerCode="true"
    />
  </target>
</project>
```


## The generated Gwt Java code ##
There are two categories of generated files:
  * The "transfer objects" code (so to speak the data structures which are transferred across the network)
  * The client proxy code (the code which creates the request and parses the response)


### The transfer object ###

```
package com.pyc.client.to;

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

}
```

### The client proxy ###

```
public abstract class MyRemoteServiceClient extends JSONRPCRootClient{
		public static final void modifyPerson(String p_url,com.pyc.client.to.Person p_params, AsyncCallback<JavaScriptObject> p_callBack) {…}
}
```

## The generated Php code ##
There are, again, two categories of generated files:
  * The transfer objects code (same as for java but on the “php side”)
  * The serveur stub code (in which you’ll set your own serveur code)

### The PHP transfer object ###

```
<?php
class Person{

<…>	
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

### The serveur stub code ###
```
<?php
jsonRPCServer::handle(new RPCServerWrapper(null,new GetPersonsServer()));

//RPC service implementation
class GetPersonsServer{
		public function getPersons(){
//!!!-----Custom code------
<…>
//!!!----------------------
	}
}
?>
```

## Using the generated code ##

### On GWT java side ###
You have to call the “getPersons” method of the client proxy (which sends the request to the server).

```
MyRemoteServiceClient.getPersons("http://..."+ l_url, new AsyncCallback<JavaScriptObject>() {
	@Override
	public void onFailure(Throwable caught) {
		…
	}
	@Override
	public void onSuccess(JavaScriptObject result) {
		JsArray<Person> l_persons = result.cast();
		…
	}
});
```

### On PHP side ###

Just put your server-side code between the “//!!!“ comments.

```
class GetPersonsServer{
		public function getPersons(){
//!!!-----Custom code------
<…>
//!!!----------------------
	}
}
```


# Further reading #
Click [here](http://code.google.com/p/gwtphprpc/wiki/InstallSample?tm=6) for further documentation.