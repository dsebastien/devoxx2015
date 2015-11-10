# Refactor your Java EE application using Microservices and containers

## Links
* https://github.com/arun-gupta/microservices
* to check: JBoss data virtualization: create virtual views on a database.
  * each microservice can access its virtualized "view".
* to check: Getting Started with Microservices DZone Refcard
* to check: microservices.io
* to check arun-gupta/elk
* to check: org.jolokia docker-maven-plugin
* to check: CQRS
* to check: https://ee.kumuluz.com
* to check: http://martinfowler.com/bliki/MicroservicePremium.html
* to check: https://www.joyent.com/blog/introducing-containerbuddy

## Monolith application
* single archive with everything (e.g., war, ear)
  * UI
  * persistence layer
  * business logic

Advantages:
* one unit, source of truth, etc
* easy to test (all required services are up)
* simple to develop

Version management:
New release (changes in the UI or persistence layer or business logic):
* everything needs to be packaged again
* any tiny change require a full packaging

Disadvantages:
* difficult to deploy and maintain
* obstacle to frequent deployments
* dependency between un related features
* makes it difficult to try out new technologies/frameworks

## Microservices
Architectural approach, that emphasizes the decomposition of applications into single-purpose, loosely coupled services managed by cross-functional teams, for delivering and maintaining complex software systems with the velocity and quality required by today's digital business.

Not a free lunch: takes effort.

Conwey's law:
Any organization that designs a system will inevitably produce an application that mimics the communication structure of the organization.

## Teams around business capability
If an app manages orders, a catalog and shipping, each block should be managed by a specific team (8-10 people)

## Characteristics of a microservice
* single responsibility principle (SRP)
  * the microservice should do one thing
* explicitly published and well defined interface
* can be independently replaced and upgraded
* you build it, you run it
* designed for failure: fault tolerance is a requirement, not a feature
  * Netflix has an "edge" API receiving all client calls and acting as a proxy/facade.
* choose the right tool for the right job
* fully automated (100%)
  * need for a full CI/CD pipeline
* sync or async messaging
  * sync: REST (different maturity models)
    * others: thrift, protobuf, avro, ...
    * approach often followed: REST between client and back-end and thrift, protobuf and the like between the back-end and other services
* smart endpoint, dumb pipes
* SOA 2.0
  * Microservices = SOA - ESB - SOAP - centralized governance/persistence - vendors + REST/HTTP + CI/CD + DevOps + true polyglot + containers + PaaS WDYT
* need for service discovery
* immutable VM

## Strategies for decomposing
* verb or usecase (e.g., Checkout UI)
* noun (e.g., Catalog product service)
* bounded context
* single responsibility principle (e.g., unix utilities)

## Towards microservices

... (schema)

Each microservice has its own database. That is the toughest part for many people. The microservice should fully own the concept.

## Aggregator Patterns

### Aggregator pattern

* service A (war) <-> cache <-> DB
* service B ...
* ...

Load balancer <-> Aggregator <-> talks with services A, B, ...

The aggregator can scale on its own and be the only one exposed outside.

### Proxy pattern

Load balancer <-> Proxy <-> talks with services A, B, ...

Can do transformations, compose results, etc.

### Chained pattern

Load balancer <-> Service A <-> Service B <-> Service C

Thing to consider: how does client goes from A to B to C. If synchronous, the chain cannot be too long.

### Branch pattern
Load balancer <-> Service A <-> Service B OR <-> Service C <-> Service C

### Shared resources
Within a chain, some resources could be shared

LB <-> A <-> B or <- C <-> D

In a chain it's okay to share resources (e.g., DB)

### Async messaging
LB <-> Service A <-> C <- QUEUE -> B


## Camel and integration
...

## QA
QA should be part of the team!

## Logging
ELK stack is recommended

## ESB
Move away from putting data or logic in an ESB

## Design principles for Monoliths
* DDD: domain driven design
* SoC using MVC
* high cohesion, low coupling
* DRY
* CoC (convention over configuration)
* YAGNI

## Advantages of Microservices
* easier to develop, understand & maintain (from 3 to 6 months to build)
* starts faster than a monolith, speeds up deployments
* local change can be easily deployed, great enabler of CD
* each service can scale on X and Z axis
* improves fault isolation
* eliminates any long-term commitment to a technology stack

If your monolith is a big ball of mud, your microservice will be a bag of dirt :)

## Service discovery
The service registry is a distributed solution. All microservices should register themselves when they come up.

If a service needs another service, it uses the service registry.

## Example
CatalogService bean that gets injected a ServiceRegistry that allows to register/unregister.

Service registries:
* Zookeeper + Curator (apache project)
  * nice but api not great
  * Curator: nice API for Zookeeper
* snoop
* etcd
* Consul
* Kubernetes: embeds a service registration/discovery service (!)

## Security
SSO and LDAP integration are

## Design considerations
* UI and full stack
  * client-side composition? (e.g., Angular)
  * service-side HTML generation?
  * thymeleaf
  * one service, one UI
* REST Services
* Event sequencing instead of two-phase commit
* API Management

## API Management
* centralized governance policy configuration
* tracking of APIs and consumers of those APIs
* easy sharing and discovery of APIs
* leveraging common policy configuration across different APIs
  * security, caching, rate limiting, metrics, billing, ...

## NoOps
* service replication (Kubernetes): Kubernetes? Mesos? Fleet? PaaS? ...?
* dependency resolution (Nexus)
* failover (circuit breaker concept)
  * hysterix
  * building fault-tolerant distributed apps ain't easy
* resiliency (circuit breaker)
* service monitoring, alerts and events (ELK)

## Data Strategy
* private tables-, schema-, or database-per-service
  * no shared tables
    * or only one service writes to a table, and others have read-only access
  * each service should have private tables, schemas
  * or even have a full DB
  * no transactions across databases
  * logital transactions within apps

## Event Sourcing
The state of the app is represented by a sequence of _events_. Shopping cart example: store events rather than raw data.

* state of the application is defined by a sequence of events
* events are stored in a document format
* events are immutable
  * delete is also implemented as an event

Event Sourcins @ Wix
* storeq-id (uuid) | store_version (id) | event_type(String) | event (JSON document)

## CQRS
* traditional: CRUD using same data model
* Command Query Responsibility Segregation
  * different model to update and read information
  * Command: change the state of the system, does not return a value
  * Query: ...
  * ...

Event Sourcing and CQRS helps with isolation of services, velocity and scalability.

## KumuluzEE
* lightweight open source framework for JEE microservices
* uses standard JEE APIs
* produces stand-alone JARs that run anywhere
* completely modular and easily extensible

Great fit for microservices:
* dependency-driven: simple to use
* pick and choose JEE components
  * also the implementations
* jar includes all deps
* ideal for running in PaaS / Docker

Components:
* containers
  * Jetty
  * coming: tomcat, undertow, grizzly
* JEE components
  * servlet, jsp, el, cdi, ...
  * EJB not supported

## Drawbacks of Microservices
* additional complexity of distributed systems (resiliency, failover, ...)
* significant operational complexity
* need a high-level of automation
* rollout plan required to coordinate deployments
* slower ROI (at the start)

http://martinfowler.com/bliki/MicroservicePremium.html
http://martinfowler.com/bliki/images/microservice-verdict/productivity.png

General recommendation:
* start with a monolith
* evaluate later if splitting is necessary

https://www.joyent.com/blog/introducing-containerbuddy

