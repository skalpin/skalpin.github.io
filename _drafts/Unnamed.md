---
layout: post
title:  "The Need for Acceptance Tests"
categories: update
---

# The Need for Acceptance Tests

## Estimates

- Giving any sort of estimate before the test steps that define "done" are complete is irresponsible.
  - But the client can't give it to us because they don't know what's possible.
  - Then timebox the work and see if you can define it within a couple of days.
  - If the client does get an estimate they will plan work around it even if you say things like "I can do this within a couple of days, but it won't be done because I don't know what you want it to actually be". They will still plan to test it as soon as it is done and have people on the clock who don't know what they are testing or how to test it.

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