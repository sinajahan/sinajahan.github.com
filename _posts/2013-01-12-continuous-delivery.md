---
layout: post
title: Continuous Delivery
code: continuous-delivery
---

{{ page.title }}
================

Let's talk about delivery

1: The Problem of Delivering Software
-------------------------------------
Antipattern: Deploying Software Manually
Antipattern: Deploying to a Production-like Environment Only after Development Is Complete
Antipattern: Manual Configuration Management of Production Environments
Every Change Should Trigger the Feedback Process
What Are the Benefits?
  Reducing Errors, Lowering Stress, Deployment Flexibility, Practice Makes Perfect

2: Configuration Management
---------------------------
3: Continuous Integration
-------------------------
Implementing Continuous Integration
￼Prerequisites for Continuous Integration
 Check In Regularly
Using Continuous Integration Software
 Bells and Whistles
Don’t Check In on a Broken Build
Always Run All Commit Tests Locally before Committing or Get Your CI Server to Do It for You
Wait for Commit Tests to Pass before Moving On
Never Go Home on a Broken Build
Don’t Comment Out Failing Tests
Take Responsibility for All Breakages That Result from Your Changes
Test-Driven Development

4: Implementing a Testing Strategy
----------------------------------
Integration Testing
Process
Part II: The Deployment Pipeline
Anatomy of the Deployment Pipeline
What Is a Deployment Pipeline?
A Basic Deployment Pipeline
Deployment Pipeline Practices
Only Build Your Binaries Once
Deploy into a Copy of Production
Commit Stage Best Practices
The Automated Acceptance Test Gate
Manual Testing
Nonfunctional Testing
Preparing to Release
Automating Deployment and Release
Automating Acceptance Tests

6: Build and Deployment Scripting
---------------------------------
Deployment Scripting
Deploying and Testing Layers
Testing Your Environment’s Configuration

7: The Commit Stage
---------------------------
Provide Fast, Useful Feedback
The Artifact Repository
Avoid the User Interface

8: Automated Acceptance Testing
---------------------------------------
Why Is Automated Acceptance Testing Essential?
Keeping Acceptance Tests Green
Deployment Tests
Acceptance Test Performance

9: Testing Nonfunctional Requirements
-------------------------------------
Programming for Capacity
Measuring Capacity
Automating Capacity Testing
Capacity Testing via the User Interface

10: Deploying and Releasing Applications
------------------------------------------------
Creating a Release Strategy
The Release Plan
Releasing Products
Deploying and Promoting Your Application
The First Deployment
Orchestration
Deployments to Staging Environments
Rolling Back Deployments and Zero-Downtime Releases
Emergency Fixes
Continuous Deployment
Log Deployment Activities
Don’t Delete the Old Files, Move Them
Deployment Is the Whole Team’s Responsibility

11: Managing Infrastructure and Environments
--------------------------------------------
Understanding the Needs of the Operations Team
Documentation and Auditing
Alerts for Abnormal Events
IT Service Continuity Planning
Managing Server Provisioning and Configuration
Provisioning Servers
Ongoing Management of Servers
Managing the Configuration of Middleware
Managing Configuration
Research the Product
Examine How Your Middleware Handles State
The People Who Do the Deployment Should Be Involved in Creating the Deployment Process
Managing Infrastructure Services
Virtualization
Managing Virtual Environments
Infrastructure in the Cloud
Platforms in the Cloud
One Size Doesn’t Have to Fit All
Criticisms of Cloud Computing
Monitoring Infrastructure and Applications
Collecting Data
Logging
Creating Dashboards

12: Managing Data
-----------------
Incremental Change
Versioning Your Database
Managing Orchestrated Changes
Rolling Back Databases and Zero-Downtime Releases
Rolling Back without Losing Data
Faking the Database for Unit Tests
Setup and Tear Down
Coherent Test Scenarios
Data Management and the Deployment Pipeline
Data in Acceptance Tests
Data in Capacity Tests

13: Managing Components and Dependencies
------------------------------------------------
Keeping Your Application Releasable
Branch by Abstraction
Dependencies
Dependency Hell
Managing Libraries
Components
The Integration Pipeline
Managing Dependency Graphs
Building Dependency Graphs
Pipelining Dependency Graphs
When Should We Trigger Builds?
Cautious Optimism
Circular Dependencies
Managing Binaries
Managing Dependencies with Maven
Maven Dependency Refactorings

15: Managing Continuous Delivery
--------------------------------
A Maturity Model for Configuration and Release Management
How to Use the Maturity Model
Project Lifecycle
Identification
Inception
Initiation
Develop and Release
Operation
A Risk Management Process
Risk Management
Risk Management Timeline
How to Do a Risk-Management Exercise
Common Delivery Problems—Their Symptoms and Causes .
Infrequent or Buggy Deployments
Poor Application Quality
Poorly Managed Continuous Integration Process
Poor Configuration Management
Compliance and Auditing
Automation over Documentation
Enforcing Traceability
Working in Silos
Change Management