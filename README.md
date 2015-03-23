Web Component Environment
=========================
This Environment makes Web Application be used like Component.


----------
Advantages
----------
Maybe this environment provides some advantages. Maybe this is useful some solution-type products.
- Divide Huge Web System
  - You can divide to 'Web Component' units.
  - You can update and deploy each component, not whole web system.
  - Each Web Component can communicate with 'Inter Component Method'.
- Integrate Configurations
  - You can manage configuration files in one folder.
- Integrate Data Files
  - You can manage data files in one folder.


------------
Installation
------------
### Create Base Folder
- Create Base Folder where you want.
  - Example: /opt/ExamSystem
- Copy Folders/Files of 'BaseExam/WebComponentEnvironment' in created base folder.
  - 'Libraries' folder must be created in Base Folder but Other folders aren't. Other folders are automatically created when Tomcat started.
- Copy 'ManagerLibrary' in 'BaseFolder/Libraries' Folder.

### Set Tomcat
- Add 'Host' Configuration in 'server.xml' of Tomcat
```xml
<Host name="wcm.mabin.info"  appBase="/opt/ExamSystem/Components"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false">
</Host>
```
- Add 'Classpath' Configuration in catalina.sh of Tomcat
```sh
CLASSPATH=/WebComponentManager/Libraries
```

### Set Components
- Add Components(.war, etc) in 'BaseFolder/Components'
- Add Configurations in 'BaseFolder/Configurations'
- Add CommonResources in 'BaseFolder/CommonResources'


------------------
Tested Environment
------------------
- Java 1.6
- Apache Tomcat 6


--------------
Need Libraries
--------------
- [ManagerLibrary (Required)](https://github.com/mabin359/WebComponentEnvironment-ManagerLibrary)
- [SupportLibrary](https://github.com/mabin359/WebComponentEnvironment-SupportLibrary)


--------
Examples
--------
- [Component Example 1](https://github.com/mabin359/WebComponentEnvironment-ComponentExam1)
  - [InterComponentMethod](https://github.com/mabin359/WebComponentEnvironment-ComponentExam1-ICM)
- [Component Example 2](https://github.com/mabin359/WebComponentEnvironment-ComponentExam2)
  - [InterComponentMethod](https://github.com/mabin359/WebComponentEnvironment-ComponentExam2-ICM)
- [Component Example 3 (Example of Support Library)](https://github.com/mabin359/WebComponentEnvironment-ComponentExam3)



=============
Web Component
=============
'Web Component' is component of Web System. Each 'Web Component' can communicates to the other components with 'InterComponentMethod' interface. 'Web Component' uses 'Web Application'. Developer can updates each component, not whole 'Web System'.

'Web Component' is provided its own storage by Manager. Each Storage is located in 'BaseFolder/Data/[PackageName]/'.

'Web Component' is also provided its own configuration by Manager. When configuration was changed, manager notify about that to each component.

Manager provides Logger. Log that logger written is located in 'BaseFolder/Logs/[PackageName].log'.


-----------------
Develop Component
-----------------
### Create Web Application.
'Web Component' uses standard 'Web Application' Project.

### Set 'web.xml' in WEB-INF (Required)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>ComponentExam1</display-name>
  <listener>
    <listener-class>canonical.name.of.ComponentClass</listener-class>
  </listener>
  <servlet>
    <!-- Servlet Settings -->
  </servlet>
  <servlet-mapping>
    <!-- Servlet Mapping Settings -->
  </servlet-mapping>
</web-app>
```

### Create 'WCManifest.xml' in WEB-INF (Required)
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<WCManifest Package="package.name.of.component">
  <ComponentName>Component Name</ComponentName>
  <UseManagerVersion>1</UseManagerVersion><!-- Required Version of Manager -->
  <IcmClasses>
    <!-- Canonical Name of ICM Class -->
    <IcmClass>canonical.name.of.Icm</IcmClass>
    <!-- You can drop Package name with dot character. -->
    <!--    (exam: '.icm.Icm') -->
    <!-- You can set more ICM Class -->
  </IcmClasses>
  <EventListenerClasses>
    <!-- Canonical Name of EventListener Class -->
  	<EventListenerClass>canonical.name.of.EventListenerClass</EventListenerClass>
    <!-- You can drop Package name with dot character. -->
    <!--    (exam: '.eventlistener.EventListenerComponent') -->
  	<!-- You can set more EventListener Class -->
	</EventListenerClasses>
  <VersionCode>1</VersionCode><!-- Component Version (Long type) -->
  <VersionName>0.1.0</VersionName><!-- Component Version (String type) -->
</WCManifest>
```

### Create 'Component' Class (Required)
```java
import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

import info.mabin.wce.manager.ComponentAbstract;
import info.mabin.wce.manager.exception.ComponentException;

public class Component extends ComponentAbstract implements ServletContextListener{
	protected ServletContext contextServlet;
	
	public static Component getInstance(){
		return instance;
	}
	
	@Override
	public void contextInitialized(ServletContextEvent event) {
		contextServlet = event.getServletContext();
		
		try {
			loadComponent(contextServlet, this);
		} catch (ComponentException e) {
			e.printStackTrace();
		}
	}

	@Override
	public void contextDestroyed(ServletContextEvent event) {
		try {
			unloadComponent();
		} catch (ComponentException e) {
			e.printStackTrace();
		}
	}

	@Override
	protected void initComponent() {}     // Coding Initializing code if you needs.

	@Override
	protected void destroyComponent() {}  // Coding Destroying code if you needs.
}
```

### Create 'EventListener' Class
If you needs EventListener, create 'EventListener' Class.
You also set in 'WCManifest.xml' about this.

Read 'EventListener' Section.


### Create 'InterComponentMethod'
If component is needed communicate other component, create 'InterComponentMethod' Class.
You also set in 'WCManifest.xml' about this.

Read 'InterComponentMethod' Section.


--------------------------
Provided Methods/Variables
--------------------------
- manifest: ComponentManifest
  - It provides contents of 'WCManifest.xml'.
    - getPackageName(): String
    - getComponentName(): String
    - getUseManagerVersion(): long
    - getVersionCode(): Long
    - getVersionName(): String
    - getListIcmClassName(): List<String>
    - getListEventListenerClassName(): List<String>
- context: ComponentContext
  - It provides objects about Component.
    - getDefaultUri(): String
    - getConfiguration(): NodeList
    - getMapIcm(): Map<String, IcmImpl>
    - getListEventListenerComponent(): List<EventListenerComponentImpl>
    - getListEventListenerConfiguration(): List<EventListenerConfigurationImpl>
    - getListEventListenerIcm(): List<EventListenerIcmImpl>
    - getResourceCommon(path: String): File
    - getResourceData(path: String): File
    - getResourceContext(path: String): File
- logger: Logger
- config: NodeList


----------------
Deploy Component
----------------
Build like 'Web Application' and just locate in 'BaseFolder/Components' folder.



=======================
Component Configuration
=======================
ConfigurationManager provides configuration of each component as 'NodeList'(DOM) Object and ConfigurationManager push event when configuration file was changed. (Checking Period: 10 seconds)

--------------------
Create Configuration
--------------------
### Create Configuration XML file
Configuration of Each Component must be formatted XML.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration Package="PackageName of Component">
	<NodeName>Value</NodeName>
</Configuration>
```

### Locate Configuration XML in 'BaseFolder/Configuration' folder



================
Component Logger
================
Logger provides logger of each component. It wrotes log in 'BaseFolder/Logs' Folder and its name is 'PackageName.xml'.

Provide Type
- Information (INFO)
  - 'info' or 'i' method.
- Debug Message (DEBUG)
  - 'debug' or 'd' method.
- Error Message or Throwable (ERROR)
  - 'error' or 'e' method.

----------
Use Logger
----------
### In 'Component' Class
You can use 'logger' variable directly.
```java
logger.info("Some Infomation");
```

### Other Class
You can get logger with this line.
```java
Logger logger = Logger.getInstance(this.getClass());
```



============================
Inter Component Method (ICM)
============================
It provides interface for communication each components. It is managed by Manager. It needs 2 parts, interface library and real class.

----------
Create ICM
----------
### Create Interface Library

#### Create 'Java' Project for Interface Library.
(like Java Library)

#### Create Interface of ICM in Interface Library (NameExample: 'IcmImpl')
Define methods of ICM
```java
import info.mabin.wce.manager.icm.IcmImpl;
import info.mabin.wce.manager.icm.exception.IcmException;

public interface IcmImpl extends IcmImpl{
	public int getSomething() throws IcmException;
	public void setSomething(int param1, String param2) throws IcmException;
}
```

#### Create Class of ICM in Interface Library (NameExample: 'Icm')
Write Code about get real class and invoke methods.

You can make 2 (more) types of it.
##### Type 1.
```java
import info.mabin.wce.manager.exception.IcmNotRegisteredException;
import info.mabin.wce.manager.icm.IcmAbstract;
import info.mabin.wce.manager.icm.exception.IcmException;

public class Icm extends IcmAbstract implements IcmImpl{

	public Icm() throws IcmNotRegisteredException {
		super("canonical.name.of.real.icmClass");
	}

	@Override
	public int getSomething() throws IcmException{
		try {
			return (Integer) this.invokeMethod("getSomething");
		} catch (IcmException e) {
			throw e;
		}
	}

	@Override
	public void setSomething(int param1, String param2) throws IcmException {
		try {
			this.invokeMethod("setSomething", param1, param2);
		} catch (IcmException e) {
			throw e;
		}
	}
}
```

##### Type 2
```java
import java.lang.reflect.Method;

import info.mabin.wce.manager.exception.IcmNotRegisteredException;
import info.mabin.wce.manager.icm.IcmAbstract;
import info.mabin.wce.manager.icm.exception.IcmException;

public class Icm extends IcmAbstract implements IcmImpl{
	private Method methodGetSomething, methodSetSomething;
	
	public Icm() throws IcmNotRegisteredException {
		super("canonical.name.of.real.icmClass");
		
		try {
			methodSetSomething = this.getMethod("getSomething");
			methodGetSomething = this.getMethod("setSomething", new Class<?>[]{int.class, String.class});
		} catch (IcmException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	@Override
	public int getSomething() throws IcmException {
		try {
			return (Integer) this.invokeMethod(methodGetSomething);
		} catch (IcmException e) {
			throw e;
		}
	}

	@Override
	public int setSomething(int param1, String param2) throws IcmException{
		try {
			this.invokeMethod(methodSetSomething, param1, param2);
		} catch (IcmException e) {
			throw e;
		}
	}
}
```

#### Create Real ICM Class in Component
```java
import canonical.name.of.IcmImpl;

public class Icm implements IcmImpl{

	@Override
	public int getSomething() {
	  // Code of getSomething Method
	  int somethingInteger = 0;
		return somethingInteger;
	}

	@Override
	public void setSomething(int param1, String param2) {
		// Code of setSomething Method
	}
}
```

#### Set ICM in Component
Register ICM to Manager, you set it in 'WCManifest.xml'.
```xml
  <IcmClasses>
    <!-- Canonical Name of ICM Class -->
    <IcmClass>canonical.name.of.Icm</IcmClass>
    <!-- You can drop Package name with dot character. -->
    <!--    (exam: '.icm.Icm') -->
    <!-- You can set more ICM Class -->
  </IcmClasses>
```


--------------------------
Use Inter Component Method
--------------------------
### Build ICM Interface Library and locate it in Using Component.
Build ICM Interface Library and locate it in 'WEB-INF/lib' of other component that use it.

### Use like Library.
```java
Icm objIcm = new Icm();
objIcm.getSomething();
```



==============
Event Listener
==============
Manager Provides 3 Interfaces of EventListener
- Event of Component (Registered/Unregistered)
- Event of Configuration (Changed)
- Event of InterComponentMethod (Registered/Unregistered)


---------------------
Develop EventListener
---------------------
### Create 'EventListener' Class
```java
import java.util.List;

import info.mabin.wce.manager.Logger;
import info.mabin.wce.manager.ComponentAbstract.ComponentManifest;
import info.mabin.wce.manager.eventlistener.EventListenerComponentImpl;

public class EventListenerComponent implements EventListenerComponentImpl{
	@Override
	public void eventRegisteredComponent(ComponentManifest manifest) {
		if(manifest.getPackageName().equals("package.name.of.target.component")){
		  // Event Code when target component was registered
		}
	}

	@Override
	public void eventRegisteredComponent(List<ComponentManifest> listManifest) {
		for(ComponentManifest manifest: listManifest){
		  // Event Code when target component was registered.
		  // This method is called when this component will be registering.
			//    example: this.eventRegisteredComponent(manifest);
			
		}
	}

	@Override
	public void eventUnregisteredComponent(ComponentManifest manifest) {
		if(manifest.getPackageName() == "package.name.of.target.component"){
			// Event Code when target component was unregistered
		}
	}
}
```

#### Set EventListener in Component
To register EventListener to Manager, you set it in 'WCManifest.xml'.
```xml
  <EventListenerClasses>
    <!-- Canonical Name of EventListener Class -->
  	<EventListenerClass>canonical.name.of.EventListenerClass</EventListenerClass>
    <!-- You can drop Package name with dot character. -->
    <!--    (exam: '.eventlistener.EventListenerComponent') -->
  	<!-- You can set more EventListener Class -->
	</EventListenerClasses>
```


===============
Support Library
===============
This provides more easy way of developing component.

[Read README.md of 'Support Library'.](https://github.com/mabin359/WebComponentEnvironment-SupportLibrary)