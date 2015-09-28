

# Developer requirements #
This tutorial requires at least a basic knowledge of GWT. It is recommended to complete the [GWT tutorial](http://code.google.com/webtoolkit/doc/latest/tutorial/index.html) before starting this one.
It is also assumed that you are familiar with PHP/Apache server configuration.

# System requirements #
PhpRpcGen has been installed and tested with the following configuration:
  * GWT 2.0.4
  * PHP 5.3.1
The following step by step explanation uses [eclipse](http://www.eclipse.org) and the [Google Plugin](http://code.google.com/eclipse/index.html), yet **there is no dependency between PhpRpcGen and eclipse**.
The generator is delivered as a simple ANT task which could be run outside eclipse.

# Creating a project #
First you must create a GWT project.

![http://gwtphprpc.googlecode.com/files/step1.png](http://gwtphprpc.googlecode.com/files/step1.png)

# Cleaning the sample project #
You can then delete the following classes which contain the java source code for the default sample GWT application:
  * GreetingService
  * GreetingServiceAsync
  * GreetingServiceImpl
  * FieldVerifier

Then, open the war/WEB-INF/web.xml file and modify it so that it looks like this:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app
    PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
  <!-- Default page to serve -->
  <welcome-file-list>
    <welcome-file>GwtPhpRpcSample.html</welcome-file>
  </welcome-file-list>

</web-app>
```

Open the java class GwtPhpRpcSample and modify it (erasing most of the content) so that it looks like this:
```
package com.test.client;

import com.google.gwt.core.client.EntryPoint;

public class GwtPhpRpcSample implements EntryPoint {
	public void onModuleLoad() {
	}
}
```

Once this modification is done and you should not see any remaining error in eclipse.

# Setting up your web server #
Configure your apache server so that "http://localhost/gwtphprpc/" will point to the content of the "war" folder. This is where the PHP files will be put.

NB: I am commonly using [XAMPP](http://www.apachefriends.org/en/xampp.html) as a desktop PHP development environment. Try it, it is very simple to install and configure !

# Writting the PHP RPC configuration file #

Creates a directory "rpc" at the root of the project. Then creates an XML file "config.xml" in this directory.
Your project view should now look like this:

![http://gwtphprpc.googlecode.com/files/step2.png](http://gwtphprpc.googlecode.com/files/step2.png)

Open the XML file and paste the following code in it, then save:
```
<?xml version="1.0" encoding="UTF-8"?>
<config>
	<transfer_objects package="com.test.client.to">
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
		<object name="Hobby">
			<attribute name="description" type="String"/>
		</object>
		<object name="Contact">
			<attribute name="identity" type="String"/>
		</object>
	</transfer_objects>
	<remote_services class="com.test.client.MyRemoteService">
		<service method="getPersons">
			<result type="Person[]"/>
		</service>
		<service method="modifyPerson">
			<params type="Person"/>
			<result type="Person"/>
		</service>
	</remote_services>
</config>
```

This code defines:
  * 3 transfer objects (data structures which convey data accross the network)
    * Person
    * Hobby
    * Contact
  * 2 remote service (methods exposing remote features to GWT client to be implemented in PHP)
    * getPersons (taking no parameters and returning an array of Person)
    * modifyPerson (which has a Person object as a parameter and as a result)

**NB: please note that the target java package for the transfer objects is   "com.test.client.to" and "com.test.client" for the remote service.
They match the package defined when we created the project (see previously). If you have chosen another package for the project then you'll need to modify the sample "config.xml" file.**

# Running the generator #
## Installing the generator ##
Create a "lib" directory within the "rpc" directory that you previously created.
Download the [binary distribution](http://gwtphprpc.googlecode.com/files/phprpcgen_bin_alpha1.zip) of the generator.
Unzip it into a "lib" directory.
## Creating the XML configuration file ##
Then creates a "build.xml" (the [ANT](http://ant.apache.org)) file into the "rpc" directory.
Open it, paste the following code and save:
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
  * keepServerCode: if true, the generator will take care not to overwrite the implementation code of the PHP remote service (see later)

## Configuring and running the ANT task ##
Right click on the "build.xml" file (the ANT one) and select "Run As > Ant build".
The console will display:
```
BUILD FAILED
```
That's normal, no worry.

Then open the eclipse ANT configuration tool.
In the menu bar select "Run > External tools > External tools configuration...".
This will open a window like this:

![http://gwtphprpc.googlecode.com/files/step3.png](http://gwtphprpc.googlecode.com/files/step3.png)

The attempt to run ANT (even if it resulted in a "BUILD FAILED") created a run configuration named "GwtPhpRpcSample build.xml".
Clicks on the "classpath" tab and add all the jars of the rpc/lib directory.

Your "classpath" tab should now look like this:

![http://gwtphprpc.googlecode.com/files/step4.png](http://gwtphprpc.googlecode.com/files/step4.png)

Then click on the "Run" button to trigger the code generation (which should end with a "BUILD SUCCESSFUL" now).

# Adding a bit of code #
## A bunch of generated code ##
Your project space (after a F5 refresh) now contains a lot of newly generated files:

![http://gwtphprpc.googlecode.com/files/step5.png](http://gwtphprpc.googlecode.com/files/step5.png)

Some Java files have been generated in the "src" directory and some PHP files in the "war" directory.

## Coding the GWT client ##
It is now time to code the GWT client.
### Modifying the GWT module configuration ###
First open the "GwtPhpRpcSample.gwt.xml" file and add the following code in the module configuration:
```
  <inherits name='com.google.gwt.http.HTTP' />
  <inherits name='com.google.gwt.json.JSON' />
```

These two "imports" are required for the RPC mechanism to work. Remember that it is based upon JSON-RPC (over HTTP obviously).
## Adding the client side code ##
Download the sample GWT java source code [here](http://gwtphprpc.googlecode.com/files/java_src_tutorial.zip).
Unzip the 3 source files (GwtPhpRpcSample.java, MainPanel.ui.xml, MainPanel.java) and copy them in the "src/com/test/client" directory of your project. The project space should now look like this:

![http://gwtphprpc.googlecode.com/files/step6.png](http://gwtphprpc.googlecode.com/files/step6.png)

These three source files simply implements the GWT GUI behavior.
Browse the content of the code to see how the generated code (especially the MyRemoteServiceClient proxy code is used).

Example: line 21 ro 26 in GwtPhpRpcSample.java
```
		mainPanel.actionButton1.addClickHandler(new ClickHandler() {
			@Override
			public void onClick(ClickEvent event) {
				callRPC1();
			}
		});
```
and
```
private final void callRPC1() {
		String l_url = "http://localhost/gwtphprpc/getPersons_server.php";
		MyRemoteServiceClient.getPersons(l_url, new AsyncCallback<JavaScriptObject>() {
			@Override
			public void onFailure(Throwable caught) {
				mainPanel.displayTextarea.setText("ERROR: "
						+ caught.getMessage());
			}

			@Override
			public void onSuccess(JavaScriptObject result) {
				JsArray<Person> l_persons = result.cast();
				StringBuffer l_sb = new StringBuffer();
				l_sb.append(dumpPerson(l_persons.get(0)));
				l_sb.append("--------------------------\n");
				l_sb.append(dumpPerson(l_persons.get(1)));
				storedPerson = l_persons.get(0);
				mainPanel.displayTextarea.setText(l_sb.toString());
			}
		});
	}

```

The method "callRPC1" is called when the button "actionButton1" is pressed.
Please note that the "MyRemoteServiceClient.getPersons" implementation is expecting an "AsyncCallback" object (see the GWT documentation).

## Adding PHP server side code ##
We need now to implement the PHP server code.
### Implementation of the getPersons remote service ###
Open the "war/getPersons\_server.php" file and paste (in the "getPersons" method body, between the "Custom code" comments) the following code:
```
$person1=new Person();
$person1->firstName="James";
$person1->lastName="Bond";
$person1->age=40;
$person1->size=1.80;
$person1->skills=array("killing bad guys");
$person1->childrenAges=array();
$person1->hobby=new Hobby();
$person1->hobby->description="women";
$person1->smart=true;

$person2=new Person();
$person2->firstName="Mister";
$person2->lastName="Bean";
$person2->age=35;
$person2->size=1.75;
$person2->skills=array("making stupid things");
$person2->childrenAges=array();
$person2->contacts=array();

$contact=new Contact();
$contact->identity="Bozo the clown";
$person2->contacts[]=$contact;

$person2->smart=false;

return array($person1, $person2);
```

This code simply creates two Person objects and returns them.

### Implementation of the modifyPerson remote service ###
Open the "war/modifyPerson\_server.php" file and paste (in the "modifyPersons" method body, between the "Custom code" comments) the following code:
```
$person->fullName=$person->firstName . " " . $person->lastName;
$person->size=$person->size-0.1;
$person->skills[]="yes " . time();
$person->childrenAges[]=10;
$person->childrenAges[]=5;
$contact1=new Contact();
$contact1->identity="Goldfinger";
$contact2=new Contact();
$contact2->identity="Scaramanga";
$person->contacts[]=$contact1;
$person->contacts[]=$contact2;
return $person;
```

This code get the Person object passed as a parameter, modifies it and returns it.

# Build and Run #
Launch the GWT compilation of the project.

Once it is completed, open your browser at the specified address: http://localhost/gwtphprpc/GwtPhpRpcSample.html

Then click first on the "getPersons" button.

![http://gwtphprpc.googlecode.com/files/step7.png](http://gwtphprpc.googlecode.com/files/step7.png)

Enter some text (to be used as the Person lastName) in the input text field and then click on the "modifyPerson" button.

![http://gwtphprpc.googlecode.com/files/step8.png](http://gwtphprpc.googlecode.com/files/step8.png)

Job done !