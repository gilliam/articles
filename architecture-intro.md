# Gilliam Architecture Guide

## 1. Introduction

The Gilliam project started after having worked with distributed
systems composed of many small services and seeing the problems and
overhead this creates when it comes to packaging, deployment and
scaling.  It is a platform for systems based on a micro service
architecture.  It is polyglot in that services written in any language
can be deployed on the platform.  It takes a declarative approach to
specifying service internal dependencies.  It is built for public and
private clouds.

Gilliam borrows code and ideas from open source projects such as
[Flynn](http://flynn.io) and Cloud Foundry. It also builds upon code
and ideas from [Heroku](http://heroku.com) and other platform as a
services.

## 2. Assumptions and Goals

### For Distributed Systems

Where other platforms are targeting *web applications* Gilliam aims to
make it easier to develop and manage services in a distributed system.
Gilliam should be designed so that it removes as much friction as
possible for maintaining a system with 50+ services.

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
deployment pipelines with canary deploys.  This means that the
platform must allow for heterogeneous deployments where there are
multiple versions of a service running at the same time.

### Sharing Services

It should be easy to share services from one system with other
systems.  This increases code reuse and encourage design of generic
components.

## 3. High Level Architecture

Gilliam is a platform for managing services in a system based on a
micro service architecture. 

The four basic constructs of Gilliam are *formation*, *process*,
*auxiliary service* and *instance*.  The *formation* is a namespace
for *instances*.  *Instances* are created out of *processes* and
*auxiliary services*.  An *instance* is assigned to a machine running
a host agent called the *executor*.  *Instances* gets assigned to
*executors* by the *scheduler*.

The whole system is bootstrapped by the *bootstrap* script.

## 4. Features

### Twelve-Factor Apps

Gilliam is built from the beginning to support the [twelve-factor
methodology](http://12factor.net/).

### Service Discovery

Gilliam comes with a really simple [service
registry](https://github.com/gilliam/service-registry) which allows
services to announce their presence and to locate other services.

Where other platforms and systems go for a service discovery system
with high level of consistency Gilliam has opted for an evental
consistent design. The assumption is that *liveness* information is
eventual consistent by its nature and regardless clients need to
handle failures and fail overs.

The service registry expose a simple REST API over HTTP.  

### Built for REST APIs

The router takes incoming HTTP requests and send them of to a service
running on the platform.  The routing table is configurable via the
`gilliam` command-line tool and allow simple matching and re-write
rules.  For example, the router can be set up to send all requests
made to `myapp.com/user/{username}` to `web.user.service/{username}`.

### Ease of Integration (Proxies)

To lower the bar to get going on the platform Gilliam comes with a
builtin HTTP proxy that allows easy access to services by simply
requesting something at `<service>.<formation>.service`.  The proxy
supports the `CONNECT` method which allows for *WebSockets* and other
protocols. 

When your service is started, the `HTTP_PROXY` environment variable
contains connection information to the proxy.

Right now only HTTP is supported but there are plans for more generic
TCP proxies to allow the use of libraries and tools that do not easily
integrate with the platform.

### Scheduler

Gilliam's scheduler is responsible for taking new instances and
assigning them to a executor.  Once assigned, an instance is never
re-assigned to another executor.  If the executor goes missing for
some reason and all the instances are lost new instances need to be
created and placed on another executor.

Where instances are placed are controlled by a *requirements and rank*
algorithm.  Each instance can have a set of *requirements* that need
to be fulfilled by executor for it to be considered.  After filtering
the remaining executors are ranked by a *rank* expression and the
first one is selected.

### Declarative Approach to Internal Dependencies

Most services need some kind of persistent storage, cache or other
kind of auxiliary service. In the `gilliam.yml` file it is possible to
specify a set of auxiliary services of different types that you want
to run along side your business code:

    auxiliary:
      _cache:
        type: redis
      _config:
        type: etcd

These services (`_cache` and `_config`) are just like the processes
you define in that they can be scaled up and down, and addressed using
`<service>.<formation>.service`.

The client (`gilliam-cli`) comes with a few builtin auxiliary service
types, but can be easiliy be extended with more.

### Turtles

Core components of Gilliam, such as the *scheduler* and the *router*,
are services running on Gilliam itself.  This allows for building
services that manages and control other services, for example auto
scalers.
