# ING

## Continuous Delivery at ING

### CI
* Code Security Assurance (CSA)
* Code Quality Assurance (CQA)
* Unit tests
* Compile & build

### Test
* Functional tests (functional regression tests)
* Functional component tests
* Nightly builds

### Integration Testing
* ...
*
### Acceptance
* Resilience tests
* Soak tests
* Load & performance tests

### Production 1
...

### Production 2
...

## Data platform
Big data:
* Hadoop
* DWH + RDBMS

Data lake:
* Hadoop file system

Near realtime data processing & analytics:
* Akka
  * build concurrent & distributed apps more easily
  * toolkit and runtime to build distributed & resilient message-drive applications on the JVM (Java or Scala)
* Kafka: high-throughput distributed messaging system
* Spark:
  * big data processing (support for Java, Python, R)
  * can run in Hadoop clusters
  * can process data in HDFS, HBase, Cassandra, ...
  * can scale up to Petabytes (e.g., do ETL and data analysis on PBs of data)
* Cassandra: highly scalable NoSQL database (Apple: 10PB of data, Netflix, CERN, eBay, GitHub, Hulu, Instagram, Reddit, Comcast, ...)

Shift towards NoSQL databases

## API platform
Every application exposes its functionalities through reusable APIs.
Helps combining data from different places/domains.

Technologies used:
* Docker
* Apache ZooKeeper/Curator: distributed key-value store, used as service discovery and configuration solution (APIs register themselves there)
* Java, Scala (JVM-based languages)
* JAX-RS
* RX: Reactive Extensions: RxJava
* Hystrix: provides fault tolerance, realtime operations and concurrency for distributed systems: https://github.com/Netflix/Hystrix
* NGINX: light & fast Web server
* Ribbon
