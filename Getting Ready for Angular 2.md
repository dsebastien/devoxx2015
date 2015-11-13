# Getting ready for Angular 2

## About
* @davyengone

## Angular 1
* not gone yet
* migration path

## Transition
* everything is a component (in AngularJS: directive component)
* use controllerAs
  * do not use $scope inside the controllers
* use the module systems to turn your application into small packages
  * your services and controllers are standalone functions or classes
* use ES2015 or TypeScript

## Modules
Use standard ES2015 modules:

```
export function TopicsController(...) {

}
...

import angular from 'angular';
import {TopicsController} from '../controllers/topicsController';

angular.module('foo.bar', []).controller('TopicsController', TopicsController);
```

```
import angular, {bootstrap, module} from 'angular';

import './modules/core';
import './modules/book';


```

Make component classes angular-agnostic to ease the migration.

Adapt all templates to support


## Gone in A2
* $scope
* Controller: becomes the basis for the Component in A2
* Directive Definition Object (DDO)
* Module (angular.module)
* JqLite
* ...

## A2
* faster change detection
* server side rendering
* native module UI (NativeScript)
* compile at build time
* removes need for many directives
* statically analyzable
* native lazy loading
* unified DI mechanism

## TS
...

