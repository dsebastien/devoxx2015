# Getting started with Docker

## Links
* github.com/docker
* github.com/javaee-samples/docker-java

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
