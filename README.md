# GraalVM native-image tool compatibility

## Intrduction

  In the scope of the [EU-FOSSSA-2 Project](https://joinup.ec.europa.eu/collection/eu-fossa-2), [hackathon](https://eufossahackathon.bemyapp.com/), there is a [list of topics](https://cwiki.apache.org/confluence/display/TOMCAT/EU+FOSSA+May+2019). Here we will discuss the *GraalVM native-image tool compatibility*

Here the initial descrition:

GraalVM native-image tool compatibility
* Should use https://github.com/apache/tomcat/tree/master/res/tomcat-maven
* Should use the JVM agent to generate reflection information https://github.com/oracle/graal/blob/master/substratevm/CONFIGURE.md
* The agent should be post CR16, to get the commit https://github.com/oracle/graal/commit/8c84d1e5d411d2515a123257c720d85c16edefee



## GraalVM

* [The Grall Project](https://www.graalvm.org/) here we will work on a Mac OS X 10.9 environement.
* [Getting Started](https://www.graalvm.org/docs/getting-started/)
* [Downloading](https://github.com/oracle/graal/releases)
* [The Github Project](https://github.com/oracle/graal)
* [GraalVM Enterprise Edition - Oracle Labs GraalVM](https://www.oracle.com/technetwork/oracle-labs/program-languages/downloads/index.html)


## Download and install

Download from [GraalVM Enterprise Edition - Oracle Labs GraalVM](https://www.oracle.com/technetwork/oracle-labs/program-languages/downloads/index.html).
The "Standard JVM" location is /Library/Java/JavaVirtualMachines/

```bash

  tar xfvo graalvm-ee-1.0.0-rc16-macos-amd64.tar.gz 

  sudo mv graalvm-ee-1.0.0-rc16 /Library/Java/JavaVirtualMachines/

```
# Configure the Environement


```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/graalvm-ee-1.0.0-rc16/Contents/Home
export PATH="$JAVA_HOME/bin:$PATH"
```

## Download Tomcat

```bash
git clone https://github.com/apache/tomcat
cd tomcat/res/tomcat-maven
```

Build tomcat, maven is required,.

```bash
mvn clean package
```

## Running Tomcat

Edit the tomcat/conf/tomcat-users.xml to have access to some of the default tomcat apps

```xml
  <role rolename="manager-gui"/>
  <user username="tomcat" password="tomcat" roles="manager-gui,admin-gui"/>
```

```bash
cd tomcat
export JAVA_HOME=/home/remm/Work/graalvm-ce-1.0.0-rc17
# export JAVA_OPTS=-agentlib:native-image-agent=config-output-dir=./target/

$JAVA_HOME/bin/java -agentlib:native-image-agent=config-output-dir=./target/ -jar res/tomcat-maven/target/tomcat-maven-1.0.jar 

# This to not over wrote the target files for each execution
# $JAVA_HOME/bin/java -agentlib:native-image-agent=config-merge-dir=./target/ -jar res/tomcat-maven/target/tomcat-maven-1.0.jar 


```

-Djava.endorsed.lib=$JAVA_HOME/lib/endorsed

Execute the agent to builld the executable file

```bash
cd tomcat
$JAVA_HOME/bin/native-image -H:+ReportUnsupportedElementsAtRuntime -H:ConfigurationFileDirectories=./target/ -jar res/tomcat-maven/target/tomcat-maven-1.0.jar


```

This will create an executable file like this: tomcat-maven-1.0

```bash
cd tomcat

./tomcat-maven-1.0

```

This is the error we have:

```bash
Exception in thread "main" javax.xml.parsers.FactoryConfigurationError: Provider com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl not found
	at javax.xml.parsers.FactoryFinder.newInstance(FactoryFinder.java:200)
	at javax.xml.parsers.FactoryFinder.newInstance(FactoryFinder.java:152)
	at javax.xml.parsers.FactoryFinder.find(FactoryFinder.java:277)
	at javax.xml.parsers.SAXParserFactory.newInstance(SAXParserFactory.java:127)
	at org.apache.tomcat.util.digester.Digester.getFactory(Digester.java:461)
	at org.apache.tomcat.util.digester.Digester.getParser(Digester.java:613)
	at org.apache.tomcat.util.digester.Digester.getXMLReader(Digester.java:785)
	at org.apache.tomcat.util.digester.Digester.parse(Digester.java:1431)
	at org.apache.catalina.startup.Catalina.load(Catalina.java:566)
	at org.apache.catalina.startup.Tomcat.init(Tomcat.java:431)
	at org.apache.catalina.startup.Tomcat.main(Tomcat.java:1396)
Caused by: java.lang.ClassNotFoundException: com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl
	at com.oracle.svm.core.hub.ClassForNameSupport.forName(ClassForNameSupport.java:51)
	at java.lang.Class.forName(DynamicHub.java:1143)
	at javax.xml.parsers.FactoryFinder.getProviderClass(FactoryFinder.java:124)
	at javax.xml.parsers.FactoryFinder.newInstance(FactoryFinder.java:188)
	... 10 more
```

This is due to missing imports in target/reflect-config.json
Basically add:
```
{
  "name" : "com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl",
  "methods" : [{"name": "<init>","parameterTypes":[] }]
},
{
  "name":"org.apache.catalina.LifecycleListener"
},
{
  "name":"org.apache.coyote.UpgradeProtocol"
},
```
And some more. The trace java -agentlib:native-image-agent=trace-output=./trace-file.json show the missing
ones but you need to add them by hands.


## ATTIC

java -Dcatalina.base=. -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=conf/logging.properties -jar target/tomcat-maven-1.0.jar --war myrootwebapp

java -Dcatalina.base=. -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=conf/logging.properties -jar target/tomcat-maven-1.0.jar --war myrootwebapp --path /path1 --war mywebapp1 --path /path2 --war mywebapp2


export JAVA_HOME=/home/remm/Work/graalvm-ce-1.0.0-rc17
export JAVA_OPTS=-agentlib:native-image-agent=config-output-dir=./target/
java -jar ./target/tomcat-maven-1.0.jar


cd target
$JAVA_HOME/bin/native-image -H:+ReportUnsupportedElementsAtRuntime -H:ConfigurationFileDirectories=./ -jar tomcat-maven-1.0.jar


```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/graalvm-ee-1.0.0-rc16/Contents/Home
export JAVA_OPTS=-agentlib:native-image-agent=config-output-dir=./target/
java -jar ./target/tomcat-maven-1.0.jar
cd target
$JAVA_HOME/bin/native-image -H:+ReportUnsupportedElementsAtRuntime -H:ConfigurationFileDirectories=./ -jar tomcat-maven-1.0.jar
```
