# Gilliam Architecture Guide

## 1. Introduction

The Gilliam project started after having worked with distributed
systems composed of many small services and seeing the problems and
overhead this creates when it comes to packaging, deployment and
scaling.  It is a platform for systems based on a micro service
architecture.  It is polyglot in that it services written in any
language can be deployed on the platform.  It takes a declarative
approach to specifying service internal dependencies.  It is built for
public and private clouds.

Gilliam borrows code and ideas from open source projects such as
[Flynn](http://flynn.io).  It also builts upon code and ideas from
[Heroku](http://heroku.com) and other platform as a services.

## 2. Assumptions and Goals

### Ease of development

The primary design goal for Gilliam is to make it easier to develop
and deploy services for a distributed system.  It should allow the
developer to focus on business logic rather than packaging, writing
Pupper or Chef manifests, handling configuration and code for managing
log files.

### For both Public and Private Clouds

Gilliam should be able to run on both a public clouds such as AWS and
in a private data center.

### Continuous Deployment Pipeline

Gilliam should be designed in such a way that it allows for continuous
deployment pipelines with canary deploys.

### Sharing Services

It should be easy to share services from one system with other
systems.  This increases code reuse and encourage design of generic
components.

## 3. High Level Architecture

Gilliam is a platform for managing services in a system based on a
micro service architecture.  Code is built with its runtime
dependencies into an immutable *image*.

The four basic constructs of Gilliam are *formation*, *process*,
*auxiliary service* and *instance*.  The *formation* is a namespace
for *instances*.  *Instances* are created out of *processes* and
*auxiliary services*.

An *instance* is assigned to a machine of some kind running a host
agent called the *executor*.  *Instances* gets assigned by the
*scheduler*.

The whole system is bootstrapped by the *bootstrap* script.

## 4. Features

### Gears

### Ease of Integration (Proxies)

### Declarative Approach to Internal Dependencies

Most services need some kind of persistent storage, cache or other
kind of auxiliary service.  ...



### Turtles

Core components of Gilliam, such as the *scheduler*, are services
running on Gilliam itself.  This allows for building services that
manages and control other services, for example auto scalers.

