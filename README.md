# GraalVM native-image tool compatibility

## Intrduction

  In the scope of the [EU-FOSSSA-2 Project](https://joinup.ec.europa.eu/collection/eu-fossa-2), [hackathon](https://eufossahackathon.bemyapp.com/), there is a [list of topics](https://cwiki.apache.org/confluence/display/TOMCAT/EU+FOSSA+May+2019). Here we will discuss the *GraalVM native-image tool compatibility*

Here the initial descrition:

GraalVM native-image tool compatibility
* Should use https://github.com/apache/tomcat/tree/master/res/tomcat-maven
* Should use the JVM agent to generate reflection information https://github.com/oracle/graal/blob/master/substratevm/CONFIGURE.md
* The agent should be post CR16, to get the commit https://github.com/oracle/graal/commit/8c84d1e5d411d2515a123257c720d85c16edefee

