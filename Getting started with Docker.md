# Getting started with Docker

## Links
* github.com/docker
* Presentation, samples, etc: github.com/javaee-samples/docker-java

## About
* create containers for software applications
* package once deploy anywhere (PODA)
* portability

## Components
* build: develop an app using Docker containers with any language and any toolchain
  * image
* ship: ship the "Dockerized" app and dependencies anywhere - to QA, teammates, or the cloud - without breaking anything
  * registry
* run: scale to 1000s of nodes, move between data centers and clouds, update with zero downtime and more
  * engine

## vs VMs
* Docker engine on top of the host OS
  * the engine runs containers which contain apps and libs, not a full-blown OS

## Dockerfile
List of commands to execute in order to build a Docker container

```
FROM fedora:latest

CMD echo "Hello world"
```

```
FROM jboss/wildfly

RUN curl -L https://github.com/javaee-samples/javaee7-hol/raw/master/solution/movieplex7-1.0-SNAPSHOT.war -o /opt/jboss/wildfly/standalone/deployments/movieplex7-1.0-SNAPSHOT.war
```

See Dockerfile reference: http://docs.docker.com/engine/reference/builder/

## Docker workflow
Client
* docker build
* docker pull
* docker run

Docker Host
* has a Docker daemon
* we can tell it to run a given image

## Union file system
Docker uses union file system, which allows to create layered images.

Example:
* base layer: Bootfs/Kernel
* Base Image: centos:7
* Image: jboss/base
* Image: jboss/base-jdk:8
* Image: jboss/wildfly

## Docker machine
Allows you to get started with Docker on OSX and Windows.

Both Docker and Docker-machine are CLIs.

To create a Docker machine:`docker-machine create --driver=virtualbox localhost`.

There are multiple drivers available.

The command above creates a Virtualbox VM with the Docker image on it.

With the Docker Machine CLI you can:
* configure Docker client to talk to host
* create and pull images
* start, stop and restart containers
* upgrade docker

Docker Machine is NOT recommended for production yet.

Providers:
* Google Cloud Engine
* EC2
* Rackspace
* Azure
* DigitalOcean

## boot2docker
Predecessor of Docker Machine. Now considered an implementation detail of Docker Machine.

## Docker toolbox
Contains:
* Docker Client
* Docker Machine
* Docker Compose
* Docker Kitematic
* Boot2Docker ISO
* Virtualbox

https://www.docker.com/docker-toolbox

## Demo
```
env | grep DOCKER
docker ps # check containers
```

Create an image:

Dockerfile:
```
...
```

To build the image: `docker build -t helloworld .`

List images: `docker images`

TIP: Busybox image is much smaller (1MB); people use that in production.

To run the built image:
```
docker run helloworld-devoxx
```

Another example with an interactive shell (assigned TTY):
```
docker run -it ubuntu sh
```

With the above, we get a shell within the container

To list stopped containers: `docker ps -a`

Let's run a WilfFly container and do port redirection: `docker run -it -p 8080:8080 jboss/wildfly`

With the above, we map the port 8080 within the container with port 8080 on the host.

To list the docker machines: `docker-machine ls`

To get the IP of a container: `docker-machine ip devoxx`

To use it: `curl http://$(docker-machine ip devoxx):8080`

## Docker Compose
Defining and running multi-container applications
Configuration defined in one or more files:
* docker-compose.yml (default)
* docker-compose.override.yml (default)

Multiple files can be specified using -f

All paths are relative

```
mysqldb:
    image: mysqldb
    environment:
        - MYSQL_DATABASE: sample
        - MYSQL_USER: mysql
        - ...
mywildfly:
    image: arungupta/wildfly-mysql-javaee7
    links:
        - mysqldb:db
```

Links:

```
data-source add --name=mysqlDS --driver-name=mysql --jndi-name=java:jboss/datasources/ExampleMySQLDS --connection-url=jdbc:mysql://$DB_PORT_3306_TCP_ADDR:$DB_PORT_3306_TCP_PORT/sample?useUnicode ...
```

Volume mapping:
```
couchbase1:
    image: couchbase/server
    volumes:
        - ~/couchbase/node1:/opt/couchbase/var
    ...
...
```

With the above, the container will store data outside

## Multi-host Networking
Create virtual networks (Software Defined Networking) and attach containers.

By default: bridge network that spans a single host. It's also possible to create an overlay network that spans multiple hosts.

Works with Swarm and Compose, but still experimental in Compose.

Pluggable: Calico, Cisco, Weave, ...

## Networking vs Links
Connect containers to each other across different physical or virtual hosts.

Containers can be easily stopped, started and restarted without disrupting connection to other containers. Can be created in any order.

```
mysqldb:
    container_name: "db"
    image: mysql:latest
    ...
mywildfly:
    ...
    environment:
       - MYSQL_URI=db:3306
    ...
...
```

## Overriding Services in Docker Compose
```
mywildfly:
    image: ...
    ports:
        - 8080:8080
```

Compose overrides file:
```
mywildfly:
    ports:
        -9080:8080
...
```

With the above we can override default values.

## Dev/Prod with Compose
production.yml
```
mywildfly:
    environment:
        - COUCHBASE_URI=db-prod:8093
    ports:
        - 80:8080
mycouchbase:
    container_name: "db-prod"
```

To run: `docker-compose up -d`

With the production version: `docker-compose up -f docker-compose.yml -f production.yml -d`

## Compose in production
Not production ready yet!

## Docker Swarm
* Native clustering solution for Docker.
* Provides a unified interface to a pool of Docker hosts.
* Fully integrated with Machine and Compose.
* Serves the standard Docker API

CLI -> Docker SWARM <_> Docker SWARM <_> Docker SWARM

Docker SWARM uses machine discovery (e.g., etcd, zookeeper, consul, libkv, ...)

Docker SWARM instances are managers; there is a single master

Docker agents are docker engines deployed on different machines and managed by the Docker SWARM. A host can only belong to one cluster.

## Node discovery
```
docker-machine create --engine-opt --cluster-advertise=<value> ...
```

...

## Master High Availability
Handle the failover of a manager instance; the Docker SWARM manager is not a single point of failure. There is a primary manager, and multiple replica instances. Requests to replica are proxied to the primary

To create a SWARM cluster: ``docker run swarm manage`.

```
--replication: enable Swarm manager replication
--replication-ttl: ...
...
```

## Scheduling Backends
3 parameters:
* based on CPU (-c)
* based on RAM (-m)
* based on the number of containers (default)

`docker-machine create -strategy <value>`

* default: spread: node with least number of running containers
* binpack: node with most number of running containers
* random: mostly for debugging

## Limiting CPU resources
docker run --help | grep cpu

...

## Machine + Swarm + Compose
...

## Persistent Storage
Data volumes can be used to persist data independent of container's lifecycle

Multiple plugins: Flocker, Ceph, ...
* can be used to create volumes remotely

```
docker volume --help
```

To create a volume: `docker volume create`

...

## docker network
New command allowing to create multiple networks (bridge or overlay).

## Docker Compose and Networking
`docker network ls`

```
mywildfly:
    image
...
```

`docker-compose logs`
`docker-compose stop`
`docker-compose rm`

`docker-compose --x-networking up -d`: creates a bridge network

## Kubernetes

### About
Open source orchestration system for Docker containers.

Provides declarative primitive for the "desired state" of an application.

* slef-healing
* auto-restarting
* schedule across hosts
* replicating
* ...

### Concepts
* pods: collocated group of Docker containers that share an IP and storage volume
  * containers always run in a pod
  * assigned IPs are ephemeral
* service: single, stable name for a set of pods, also acts as Load Balancer
* label: used to organize and select group of objects
  * everything gets a label
  * labels are stored in etcd
* replication controller: manages the lifecycle of pods and ensures specified number are running
* node
  * docker
  * kubelet: talks with the master
* master
  * kubelet: talks with other kubelets
  * single point of failure!

### Master High Availability
...

### Kubernetes vs Docker Swarm
Both are competitors!

Comparison: https://github.com/javaee-samples/docker-java/blob/master/chapters/docker-vs-kubernetes.adoc

Overall, Kubernetes wins for now

### kubectl
Script that controls the Kubernetes cluster manager.
* get: `kubectl get pods` (or nodes)
* get: `kubectl get services`
* get: `kubectl get rc`
* create: `kubectl create -f <filename>`

`echo $KUBERNETES_PROVIDER` -> vagrant (easiest): vagrant creates VMs

## Kubernetes config

```
apiVersion: v1
kind: Pod
metadata:
    name: wildfly-pod
    labels:
        name: wildfly
spec:
    containers:
        - image: jboss/wildfly
        name: wildfly-pod
        ports:
            - containerPort: 8080 # port NOT exposed on the host
```

```
apiVersion: v1
kind: Pod
metadata:
    name: wildfly-pod
    labels:
        name: wildfly
spec:
    replicas: 2
    template:
        metadata:
            labels:
                name: wildfly
        spec:
            containers:
                ...
```

`export KUBERNETES_PROVIDER=vagrant`

Easily install Kubernetes: `get.k8s.io`

A pod with one container:
```
apiVersion: v1
kind: Pod
metadata:
    name: wildfly-pod
    labels:
        name: wildfly
    ...
...
```

### Services
Set of pods put together and exposed with a single IP. Helps with load balancing (simple TCP/UDP LB).

Creates environment variables in other pods (like docker links but across hosts).

Stable endpoint for pods to reference: IP address that doesn't change

Creation:
* node
  * pod
    * Docker (WildFly)
  * pod
    * Docker (MySQL)
  * mysql service

```
...
    labels:
        name: mysql-pod
        context: docker-k8s-lab
```

Usage to create service:

```
...
    selector:
        name: mysql-pod
        context: docker-k8s-lab
...
```

### Services across two nodes
You cannot control whether pod x goes to node x or y. You can affect the location with affinity, etc but not assign directly to a node.

### Replication controller
Ensures that a specified number of pod "replicas" are running.
* Pod templates are cookie cutters
* rescheduling
* manual or auto-scale replicas
* rolling updates:
  * suppose there is v1 of the app running
  * and v2 on another
  * scale down one side
  * scale up the other side for the transition
  * ...

Recommended to wrap a Pod or Service in a Replication Controller (RC)

By default, pods will always restart (it can be overridden).

### Replication Controller Configuration
...

### Replication Controller
Nothing but a resource.
Suppose there are 3 nodes + the master with a replication controller.

With the automatic rescheduling, if a node goes down, the RC will reschedule it to run elsewhere.

`kubectl.sh scale --replicas=3 rc wildfly-rc`

### Sample production deployment
With 10 nodes having each 32 cores and 160GB they can run: 400 (normal) to 600 (peak) containers.

### Health checks
* restart a pod, if wrapped in a replication controller
* application-level health checks (e.g. HTTP status codes, container exec, TCP socket alive, ...)
* ...

### Kubernetes using docker
```
etcd:
    image: gcr.io/google_containers/etcd:2.0.9
    net: "host"
    entrypoint: /usr/local/bin/etcd --addr=127.0.0.1:4001 --bind-addr=0.0.0.0:4001 --data-dir=...
master:
    ...
...
```

* Host OS
  * Docker Machine
    * Kubernetes (Docker Compose)
      * etcd
      * Master
        * Pod
          * container <--- woohoooo
      * Proxy

http://kubernetes.io/gettingstarted/

Tip: use ELK (Elastic Search, LogStash, Kibana): https://www.elastic.co/webinars/introduction-elk-stack

## Docker and Maven
Build a Docker image using a specific Maven profile

Link: https://github.com/javaee-samples/javaee7-docker-maven

## Docker and IDE support
* Eclipse: https://www.eclipse.org/community/eclipse_newsletter/2015/june/article3.php
* IntelliJ: https://blog.jetbrains.com/idea/2015/03/docker-support-in-intellij-idea-14-1/
  * Docker Integration plugin

 ## Arquillian
 Arquillian supports Docker (Arquillian Cube)

