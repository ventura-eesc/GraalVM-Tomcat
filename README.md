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


## ATTIC

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/graalvm-ee-1.0.0-rc16/Contents/Home
export JAVA_OPTS=-agentlib:native-image-agent=config-output-dir=./target/
java -jar ./target/tomcat-maven-1.0.jar
cd target
$JAVA_HOME/bin/native-image -H:+ReportUnsupportedElementsAtRuntime -H:ConfigurationFileDirectories=./ -jar tomcat-maven-1.0.jar
```
