# Refactor your Java EE application using Microservices and containers

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

To check: JBoss data virtualization: create virtual views on a database. Each microservice can access its virtualized "view".

### Async messaging
LB <-> Service A <-> C <- QUEUE -> B

To check: Getting Started with Microservices

