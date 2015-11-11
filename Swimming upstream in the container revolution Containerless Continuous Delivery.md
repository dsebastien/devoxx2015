
# Swimming upstream in the container revolution Containerless Continuous Delivery

## Links
* @bjschrijver
* OWASP dependency check: https://www.owasp.org/index.php/OWASP_Dependency_Check

## Approach for DevOps
* expert team formation: developers, operations and cloud expertse
  * choose/prepare the technology stack
* keep the shop open: build a complete new setup to allow development teams to transform to the new situation at their own pace
* define principles: key points

## Principles
* master branch is always releasable
  * don't merge it until it's done
  * every change is developed and tested in a feature branch
  * done, tests ran, manual & performance testing performed, ...
* each commit is tested extensively
  * unit & integration testing
  * mutation testing: forming small bytecode change in production code and when doing so, some tests should fail
  * end to end testing using Fitnesse and browserstack
  * automated performance tests using Gatling
  * layout testing using Galen
  * Sonar for quality & coverage reporting
* every delivery step is a Jenkins job
  * manage builds, tests, QA and deployments from a single place
  * allows to easily create pipelines
  * plugin to scale jenkins automatically (jenkin slaves)
    * if not needed, slaves are disposed of
* deployments are roll-forward only
  * after deploying 5 new features, when one has an issue, why roll back 4 good features?
    * instead, roll out a fix quickly
* infrastructure as code for everything
  * hands off: no logging in to servers should ever be necessary
  * puppet for scripting the servers configuration
  * instead of touching servers, just update the puppet recipes
  * servers are disposable: throw away and rebuild
  * use persistent volumes for data that should be kept when a server is scratched
  * 3 steps to build as server
    * base image (os, java, ...)
    * configure
    * register with Jenkins to get Jenkins to deploy
    * for databases
      * mongodb cluster
      * software upgrade
        * change version number in puppet
        * drop server
        * the server gets re-created
* auto scaling groups for everything (amazon-specific)
  * even when you don't need to scale yet
  * automated scaling (e.g., based on CPU usage)
  * minimum requirements (e.g., at least X instances)
  * when deploying, there's the issue of discovering the available servers to deploy tool: they use Amazon APIs
* no downtime in production
  * maintenance windows don't make sense to modern end users anymore
  * during deployments, they temporarily increase the capacity so as to keep the same capacity during the deployment
  * they only deploy what has changed
  * DB
    * schema less (so no updates needed)
    * they try to avoid breaking DB changes
* eyes and ears in production
  * work proactive, not reactive
  * ELK (Elastic Search, Logstash & Kibana): centralize all application logs
  * monitor APIs to keep an eye on response times, accesses, ...
  * push model for monitoring: send mail, inform when issues occur
  * pull model for monitoring:
* repeating tasks are executable for ALL team members
  * specialisms are OK, but only for incidental tasks
  * repeating tasks such as viewing logs and doing deployments must be common jobs
  * avoids spofs
  * shares knowledge
* DevOps teams work on a self service basis
  * give teams freedom to work in a way that works for them
  * differences between teams are OK
  * a team that's dependent on external help is NOT OK

## Challenges and lessons learned
* Amazon has limits (e.g., number of instances, load balancers, ...) and hard limits (e.g., API usage throttling)
* Resistance: don't assume that cultural change won't be an issue. It will
* Communication is key: you need to be really clear about
  * where things are going
  * why things are happening
  * when this impact teams
* puppet
  * from a puppet point of view, everything is production
* don't depend on the availability of Ops experts
  * this kills team progress
  * all persons in the team need to do the operational work too
  * the ops expert focuses on improving the operational tasks
* developers need to step up their game
  * not all developers are comfortable with managing infrastructure and middleware
    * you build it -> you run it

## Business benefits
* availability: auto scaling and pro-active monitoring boost availability a lot
* continuity: automated provisioning makes sure that every environment can be re-built from scratch in minutes
* agility: higher levels of automation results in shorter release cycles and faster time to market
* cost reduction: lower operational costs due to scheduling and scaling. Lower maintenance costs due to high degree of automation
* better reaction speed: faster problem analysis and solution

## Looking ahead
* better monitoring and dashboards: getting the teams the information they need, readily available on a dashboard visible from their desks
* continuous performance testing: daily performance runs on test environments and continuous end-user performance and monitoring in production
* continuous security testing
  * OWASP dependency check: check maven deps for security issues
* automated resilience testing: make sure that things will fail by making them fail yourself to ensure that the systems react as they should
  * chaos monkey
