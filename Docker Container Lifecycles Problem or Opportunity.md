# Docker Container Lifecycles Problem or Opportunity

## Links
* jFrog developer advocate
* artifactory, bintray, mission control

## Promotion pipeline
* development builds
* dev integration tests
* integration tests
* staging
* pre-prod
* production

Quality gates and visibility:
* decentralized dev environments

## Dockerfile are fragile
* latest versions used, variability when building

## Pipeline visualization
* ...

## Anatomy of Docker tag
The tag includes a reference to the registry host.

Ways to avoid the issue of having a single registry reference:
* minimize number of repositories docker interacts with
* deploy to virtual (backed by dev repository)
* promote within artifactory
* resolve from virtual (production-ready images)

Virtual repositories with Artifactory:
* docker-dev-virtual
* docker-dev-local
* docker-prod-virtual
* docker-prod-local
* docker-hub-remote

## Promotion metadata
* what to promote (which build)
* where from, where to
* status (staged, released)
* user (who did it)
* comments
* custom metadata
