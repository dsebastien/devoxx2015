# Process-driven applications

## About
* jBPM project lead
* works at redhat
* @krisverlaenen

## Business Process
Why modelize business processes?
* visibility
* monitoring
* higher-level
* continuous improvement
* agility

Focus on:
* authoring of processes
* monitoring
* ... and the execution

## Process-driven applications
Goal: help develop your application

## Evolution
Evolution of jBPM from a framework to a tool.

* started with a process engine (BpmPaas, Process Execution Server)
* Data Modeler, Form Modeler, BAM
* Process Management Console
* Process, Rules and CEP
* BPMN 2.0 standard
* Core Process Engine

The process engine can be embedded in an application:
* close integration: java pojo, persistence & transactions
* scales with the application

The engine (process execution server) can also be used as a service:
* multiple applications can interact with it through the remote API (RESTful)

Another architecture is to deploy multiple independent engines

Domain-Specific:
* use higher-level constructs specific to your domain
* business analyst collaboration
  * they can participate
  * understand the end result

Customization
...
