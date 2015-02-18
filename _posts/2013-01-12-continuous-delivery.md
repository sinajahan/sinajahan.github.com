---
layout: post
title: Continuous Delivery Book Review
abstract: How much time for deploy a single line of code change to production?
code: continuous-delivery
tag: top
---

{{ page.title }}
================
{{ page.date | date_to_string }}

I enjoyed this book by Jez Humble very much. Here are some notes that I found interesting while reading it.

Your product manager wants to have a demo of latest feature and all of a sudden there is stress in you team. All dev team are in the middle of developing something and your senior architect is trying to configure the environment last minute. You need a lot of time and head up in order to have a working software.

> How long would it take your organization to deploy a change that involves just one single line of code?
>
> Mary Poppendieck

The same thing happens when you are trying to push a critical fix to production. You have to jump through a lot of hoops
to get it deployed and fixed and meanwhile you have angry customers.

The fact is software delivers no revenue until it is in the hands of its users. Our aim is to make the delivery of
software from the hands of developers into production a reliable, predictable, visible and largely automated process
 with will understood, quantifiable risks.

Here are a couple of ideas to help you build the same culture in your team.

The Problem
-------------------------------------
The day of a software release tends to be a tense one. The reason for the nervousness should be clear. There is quite a
lot to go wrong in this process. It should be an easy task to start your application in a new environment.

**Don't do the deployment manually based on step by step documentation.** The problem with the documentation is it has to make assumption about the level of knowledge of the reader.
In reality this will not work without pain.

**Don't configure the environment manually.** There are lot of tools for automating these things such as Puppet, Chef, etc. Use one.

> In software, when something is painful, the way to reduce the pain is to do it more frequently.

** New Developer's workstation setup should be easy.** It should be possible for a new team member to sit down at a new workstation,
check out the project's revision control repository, and run a single command to build and deploy the application to a any
accessible environment, including the local development workstation.

**Definition of Done: Done means Released.** A team should have a common definition of done. there shouldn't be any 90% done.
That doesn't mean anything. If it is done, it is released.

It shouldn't take you a long time if your production environment fails catastrophically.

Managing your project's artifacts
------------------------
You should be able to reproduce any of environments (including OS, patch, network configs, software stack and application).
Use Trunk development and feature toggling instead of feature branching.
The binaries should be same for all environments. Keep your configuration variables on the box. One of the best places is
Environment variables.
It should always be cheaper to create a new environment than to fix an old one.

Continuous Integration
-------------------------
Make a habit in your team to **Check In Regularly**. Try to have everyone take baby steps. While doing TDD whenever they are
in a green state they should check in. The running time of your CI server should be **between 5 to 10 mins **so everyone gets feedback
 early enough is something is broken. Add enough Bells and Whistles to make that happen. Usually an email notification in enough.
Have a culture of not going home if you have broken the build.
Version your data base too.


Tests
=====
Your application should have different layers of testing:

Unit testing
------------
Lowest level of testing on method level and class level. Avoid DB, UI, HTTP, API and
any other interaction points.

User acceptance testing
-----------------------
Each user story should have at least one happy path test.
These tests make sure the business value of the story in captured by implementiotion.
Try to avoid hitting UI as it changes more often in these tests.
An common anti pattern in here is going into too much detail.
These tests should be high level and use domain model language of the app.

Integration Testing
-------------------
Tests the points of integration with other parts of applications and APIs.
This was the main motivation of CI.

Manual Testing
--------------
UAT testing and manual expiatory tests that can not be automated.
Humans are expensive resources. You QA team should not do repetitive happy path testings.
Automate those and have QA/UAT team try to break the system or try to test the user experience
of the system.

Nonfunctional Requirement Testing
---------------------------------
Try to have NFRs as precise as possible.
Tests such as capacity test and page load times. There are different types of capacity testing:
Scalability tests, Longevity tests, Throughput tests, Load testing.
Automate these tests in early phases of the project and make sure as project is progressing
these expectations are still being made.

Deployment/Smoke tests
----------------------
These are automated tests to show if the deployment went ok or not.
It will usually touch every part of application in a shallow way.

> Continuous Delivery is more that just a new delivery methodology.
> It is a new paradigm for running a business that depends on Software.
