---
layout: post
title:  "The Need for Acceptance Tests"
categories: update
---

# The Need for Acceptance Tests

## Task Estimates

So the client asked you for an estimate and you don't feel that you have enough information to give one. In this instance I am assuming the client has decided that the task at hand is on their *critical path*. When the client is building a schedule, they are trying to determine what tasks are dependencies for other ones and need to know how long tasks will take in order to complete. The problem arises if the client has not spent any time defining *done* and instead has decided to define how the problem needs to be solved. In their mind there is no problem to solution through because it is not complex, and all you need to do is translate the english into code. In these cases the client has no idea what else is going on in the system, and often they don't know how they are going to test what you are going to write. 
Giving any sort of estimate before the test steps that define "done" are complete is irresponsible. This includes pointing, tshirt sizing, or SWAG estimates. If you don't know what the client will accept as complete... what are you trying to complete?
Giving an estimate in this state is also very risky. When you give the work a time frame, the client will schedule people to be ready as soon as the work is complete to validate your work.

>If you don't know what the client will accept as complete... what are you trying to complete?

- If you need to spend some time discovering what is possible to give options to the client, then you need to **timebox** the spike. This will give them at least a way to know when you will come back with more information. Then as a team you can reevaluate the situation.


## Acceptance Test Steps

- Clear repeatable steps are **required** if the work is to be
  - estimated
  - progress tracked
  - developer tested
  - qa tested
  - uat tested
  - bugs tracked against
- The definition of a bug is something that occurs that is unexpected after following the given test steps. If there are no test steps there are no bugs.
- A change is something new that is being requested that was unknown to the consultant.
- Repeatable test steps are the documentation needed to determine if the new request from the client is a **change** or a **bug**. This is important because bugs are generally considered the fault of the programmer, while new changes are a fault of the client.

## Sprint size

- If your developers are starting a sprint leisurely and then cramming at the end, then the sprint length should be shortened. This should help prevent "analysis paralysis" and force the development team to ask for help sooner than at the end of the sprint. It will also force some of the user stories to be shortened which are generally easier to estimate accurately. 