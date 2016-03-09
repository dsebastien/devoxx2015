# Arquillian

## Goals
* tests should be portable
  * on any supported container (e.g., Tomcat, TomEE, IBM WebSphere, ...)
* tests should be executable
  * on the developer IDE
  * on the build tool
  * within existing test frameworks

Focus on integration and functional tests

## Steps
* select a container
* start or connect to a container
* package test archive and deploy to container
* run tests in-container
* capture and report test results
* undeploy test archive and stop or disconnect from the container

Arquillian builds the application, create an archive and sends it to the container.
Arquillian uses a ShrinkWrap API to create archives (e.g., jar, war, ear, rar)

Classes added to the archive using ShrinkWrap also come with their dependent classes (i.e., no need to list ALL classes, only the top ones).

## Database setup
A plugin can initialize a test database before tests are executed and drop it away once the tests have been executed.

## Docker support
Arquillian Cube: runs your tests on Docker containers
