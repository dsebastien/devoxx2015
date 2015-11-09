# Getting started with Docker

## Links
* github.com/docker

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











