# depcheck-test

This project is created to document a false positive, possible bug in [OWASP Dependency Check maven plugin](https://github.com/jeremylong/DependencyCheck).

This example project consists of two modules:
* depend: Creates an empty jar(test-mongodb-dependency-2.17.2.jar) depending on org.mongodb:mongodb-driver:3.6.4. This version of mongodb-driver does not have known vulnerabilities. It is crucial for the test that this jar has version 2.17.2.
* failing: Creates an empty jar depending on test-mongodb-dependency:2.17.2. 


## Build
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
Referencing identifier **cpe:/a:mongodb:mongodb:2.17.2** is wrong: This project does not have a dependency to any mongodb-library versioned 2.17.2.

Running `mvn dependency:tree` produces the following output:
```
 org.codehow:should-fail:jar:1.0-SNAPSHOT
 \- org.codehow:test-mongodb-dependency:jar:2.17.2:compile
    \- org.mongodb:mongodb-driver:jar:3.6.4:compile
       +- org.mongodb:bson:jar:3.6.4:compile
       \- org.mongodb:mongodb-driver-core:jar:3.6.4:compile
```

Following seems to be the case:
1. Name of dependency contains a name that can be resolved to a false vendor/product-combination. In this case test-mongodb-dependency causes dependency-check to resolve mongodb as a product/vendor
2. The dependency must refer to an artifact from this vendor. In this case relys the test-mogo-dependency jar on org.mongodb:mongodb-driver:4.6.3
3. There must exist a known vulnerability in NVB on the product/vendor combined with the version of the module being checked. In this case: test-mongodb-dependency:2.17.2 leads to cpe:/a:mongodb:mongodb:2.17.2

(If I change the name of test-mongodb-dependency to test-mongo-dependency the entire project passes the test).
