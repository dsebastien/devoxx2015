# Bridging the communication gap

## Test automation
Should start right at the beginning: write tests that FAIL.

...

## BDD
Behavior Driven Development
* == TDD done right
* think behavior: 'should' instead of 'test'
* ubiquitous language
  * given <some initial context>
  * when <an event occurs>
  * then <ensure some outcomes>

Business analyst, developer, tester together as one team.

## Tools
* jBehave
* Fit
* Spock
* givwenZen
* jNario
* EasyB
* FitNesse
* Cucumber
* Robot framework
* ...

Too many ones!

Selection criteria:
* activity (< 1y)
* maturity (>= 3y)
* java

Results (today):
* concordion
* cucumber
* jbehave
* robotframework
* serenity
* scalatest
* specs2
* spock
* (gauge)
* (greenpepper)

Choice depends on:
* size of the project
* customer involvement (should the customer also write specs?)
* skills of the team
* goals
  * acceptance testing?
  * unit testing?
  * ui driven?
  * ...
* ...

Categories:
* given when then: cucumber, jbehave
* html specifications: concordion
* scala, groovy: scalaTest, specs2, spoc
* UI: robotframework
* reporting: serenity

## BDD
* given
* when
* then

Cucumber includes reporting and automates some tedious work.
First we define the test scenario, then we implement it.

## Conclusion
Tools are just tools... The approach is the most important aspect: TDD (ATDD, Specification by example, ...)

Tools can improve communication by providing living documentation.
