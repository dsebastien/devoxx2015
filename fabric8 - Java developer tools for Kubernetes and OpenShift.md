# fabric8 - Java developer tools for Kubernetes and OpenShift

## Author
* Roland Hub (Twitter: @rol4nd)

## Considered stack
* Docker
* Kubernetes
* OpenShift v3

Creates a full PaaS solution.

## Kubernetes
An orchestration solution for Docker containers.

* pods: collection of one or more Docker containers
* replication controller: creates and takes care of pods
* services: proxy for a collection of Pods
* labels: grouping and organization of objects

Kubernetes supports JSON and YAML configuration

## fabric8
It's orthogonal to the stack. fabric8 provides services and tools for Kubernetes and OpenShift

## OpenShift
History:
* started in 2011: PaaS from RedHat
* three variants
  * online: public PaaS
  * enterprise: private PaaS (subscription required!)
  * origin: community PaaS (no subscription needed)
* v3: complete rewrite on top of Kubernetes

Stuff that OpenShift adds:
* the build aspect to Kubernetes
  * how to get source code, build and create images from that
  * ...
* infrastructure services
* application templates
* developer and operations tools

Templates:
...

## fabric8
History:
* started in Fuse ESB by FuseSource
* Fabric: extension for managing many ESBs
* RedHad acquired FuseSource in 2012
* Fuse ESB -> JBoss Fuse
* Fabric (closed) => fabric8 (open source)
* fabric8 1.x: based on Zookeeper as central view of the system
* fabric8 2.x sits on top of Kubernetes

Themes:
* DevOps
* Management
* Quickstarts
* iPaaS
* Tooling

## DevOps
* jenkins based Continuous Integration
* Continuous Delivery
* reusable jenkins workflow library
* pick-and-choose
  * Nexus
  * Gogs: Git repository
  * SonarQube: code quality
  * hubot: notifications and approvals
  * chaos monkey: resilience tester

## Management
Fabric8 management console:
* apps, services, replication controller, ...
* ...

## Quickstart
Easy to try out. Full featured Vagrantfile:
* latest OpenShift Origin and Kubernetes Version
* installation of fabric8 Console + Route

Gofabric8:
* installs Routes, Secrets and Volumes
* pre-fetching of fabric8 microservice images

Sample projects:
* http://github.com/fabric8io/ipaas-quickstarts
* also available as archetypes

## iPaaS
* camel tooling: visualization and management of Came Routes
* fabric8 MQ
  * smart messaging proxy
  * autoscaling of ActiveMQ
* API management
  * www. apiman.io
  * quotas, rate limiting, security and metrics for arbitrary microservices
  * ...

## Tooling
* Maven plugins
  * building of Docker images
  * create Kubernetes / OpenShift API objects
  * create OpenShift routes for all services
* Kubernetes Remotes API (e.g., used by Jenkins Kubernetes Plugin)
* CDI extension for service injection
* JBoss Forge addons

## Demo

