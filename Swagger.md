# Swagger

## Swagger
* languague-agnostic interface to REST APIs
* allows to discover ...
* ...

## Techonology stack for the demos
* Spring, Spring Boot, ...
* Springfox: providing dynamic api-docs
* Swagger UI: accessing api using a browser
* Swagger Codegen: generating client side stubs

## Big picture
rest api -> springfox (/v2/api-docs/) -> Swagger-UI (/swagger/index.html)
rest api -> springfox -> Swagger Codegen -> client code (Java, nodejs, ...)

## Springfox
* provide complete api docs for every @RESTController
* services
* supported verbs (get, post, ...)
* request parameters ...
* ...

## Swagger UI
* JS application for accessing your complete REST API using a browser
* lists all services directly from the (dynamic) api-docs
* ...

## Swagger Codegen
* CLI tool to generate client stubs (customizable)
* supports Java, C#, dart, groovy, JaxRS, nodejs, objective-c, perl, php, python, ruby, scala, ...

## Why generate client code
* ensures consistency of your client code with the API
* makes code completion possible (for service methods & model classes)
* allows developers to read description for your operations and models in the IDE
* compilation errors if the API breaks with newer versions!

## Swagger
* requires annotations in the production code
* Swagger UI is reallyt nice
...

## Springfox
...

## Swagger-UI
* ship together with your REST service
* makes accessing the service easier
* ...

## ...
...

## Swagger users
* Paypal
* Microsoft
* Amazon
* ...

...
