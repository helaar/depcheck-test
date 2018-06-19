# depcheck-test

This project is created to document a false positive, possible bug in [OWASP Dependency Check maven plugin](https://github.com/jeremylong/DependencyCheck).

This example project consists of two modules:
* depend: Creates an empty jar(test-mongodb-dependency-2.17.2.jar) depending on org.mongodb:mongodb-driver:3.6.4. This version of mongodb-driver does not have known vulnerabilities. It is crucial for the test that this jar has version 2.17.2.
* failing: Creates an empty jar depending on test-mongodb-dependency:2.17.2. 

```
$ mvn clean install
:
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------


$ mvn org.owasp:dependency-check-maven:3.2.1:check
:
[WARNING]

One or more dependencies were identified with known vulnerabilities in should-fail:

test-mongodb-dependency-2.17.2.jar (cpe:/a:mongodb:mongodb:2.17.2, org.codehow:test-mongodb-dependency:2.17.2) : CVE-2014-8180


See the dependency-check report for more details.
:

```
