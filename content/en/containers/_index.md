---
date: "2019-04-21T22:00:00-04:00"
title: "Containers"
featureconfig:
  title: "Containers"
  description: "A high level overview of Containers."
  status: "Containers"
  weight: 2
lastmod: "2019-04-21"
layout: single
linktitle: containers
authors: ["sylus"]
categories:
  - containers
tags:
  - containers
  - docker
slug: containers
toc: true
---

{{< youtube EnJ7qX9fkcU >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and container image creation as well as execution.

* [Container Basics][katacoda-containers]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Overview

Containers yield the following benefits to Government:

<div class="col-xs-6 col-sm-6 col-md-6">
  <ul>
    <li>Platform independence</li>
    <li>Resource efficiency and density</li>
    <li>Effective isolation and resource sharing</li>
    <li>Speed</li>
  </ul>
</div>

<div class="col-xs-6 col-sm-6 col-md-6">
  <ul>
    <li>Immense and smooth scaling</li>
    <li>Operational simplicity</li>
    <li>Developer productivity</li>
    <li>Development pipeline</li>
  </ul>
</div>

A container is a standard unit of software that packages up code and all of its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application this includes the code, runtime environment, tools / libraries and configuration.

Container images become containers at runtime and in the case of Docker containers - images become containers when they run on Docker Engine. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging environments.

### Platform independence: Build it once, run it anywhere

A major benefit of containers is their portability. In particular containers help to facilitate a cloud native approach via a microservices architectural design pattern.

A container wraps up an application with everything it needs to run, like configuration files and dependencies. This enables you to easily and reliably run applications on different environments such as your local desktop, physical servers virtual servers, testing, staging, production environments and public or private clouds.

This portability grants organisations a great amount of flexibility, speeds up the development process and makes it easier to switch to another cloud environment or provider, if need be.

### Resource efficiency and density

Since containers do not require a separate operating system, they use up less resources. While a VM often measures several gigabytes in size, a container usually measures only a few dozen megabytes, making it possible to run many more containers than VMs on a single server.

Since containers have a higher utilisation level with regard to the underlying hardware, you require less hardware, resulting in a reduction of bare metal costs as well as datacentre costs.

### Effective isolation and resource sharing

Although containers run on the same server and use the same resources, they do not interact with each other. If one application crashes, other containers with the same application will keep running flawlessly and won’t experience any technical problems. This isolation also decreases security risks: If one application should be hacked or breached by malware, any resulting negative effects won’t spread to the other running containers.

### Speed: Start, create, replicate or destroy containers in seconds

As mentioned before, containers are lightweight and start in less than a second since they do not require an operating system boot. Creating, replicating or destroying containers is also just a matter of seconds, thus greatly speeding up the development process, the time to market and the operational speed. Releasing new software or versions has never been so easy and quick. But the increased speed also offers great opportunities for improving customer experience, since it enables organisations and developers to act quickly, for example when it comes to fixing bugs or adding new features.

### Immense and smooth scaling

A major benefit of containers is that they offer the possibility of horizontal scaling, meaning you add more identical containers within a cluster to scale out. With smart scaling, where you only run the containers needed in real time, you can reduce your resource costs drastically and accelerate your return on investment. Container technology and horizontal scaling has been used by major vendors like Google and Twitter for years now.

### Operational simplicity

Contrary to traditional virtualisation, where each VM has its own OS, containers execute application processes in isolation from the underlying host OS. This means that your host OS doesn’t need specific software to run applications, which makes it simpler to manage your host system and quickly apply updates and security patches.

### Improved developer productivity and development pipeline

A container-based infrastructure offers many advantages, promoting an effective development pipeline. Let’s start with one of the most well-known benefits. As mentioned before, containers ensure that applications run and work as designed locally. This elimination of environmental inconsistencies makes testing and debugging less complicated and less time-consuming since there are fewer differences between running your application on your workstation, test server or in production environment. The same goes for updating your applications: you simply modify the configuration file, create new containers and destroy the old ones, a process which can be executed in seconds. In addition to these well-known benefits, container tools like Docker offer many other advantages. One of these is version control, making it possible for you to roll-out or roll-back with zero downtime. The possibility to use a remote repository is also a major benefit when working in a project-team, since it enables you to share your container with others.

## Containers and Governance

The following is a presentation by Aidan Feldman, formerly of @18F given to the Government of Canada: Cloud Native Meetup in March of 2019.

Its talks about the `cloud.gov` experience in trying to get departments to embrace containerization and significant hurdles that arose.

{{< youtube foDJfHoW1HQ >}}

## Container Design Patterns

The principles for creating containerized applications listed below use the container image as the basic primitive and the container orchestration platform as the target container runtime environment. Following these principles will ensure that the resulting containers behave like a good cloud native citizen in most container orchestration engines, allowing them to be scheduled, scaled, and monitored in an automated fashion.

### Structural Patterns

The patterns in this category are focused on the structure and relationships among containers in a pod to satisfy the different use cases.

One way to think about container images and containers is similar to classes and objects in the object oriented world. Container images are the blueprint from which containers are instantiated. But these containers do not run in isolation, they run in other abstractions such as pods and namespaces that provide unique runtime capabilities.

* **Sidecar**: Describes how to extend and enhance the functionality of a preexisting container without changing it
* **Initializer**: Describes fature that allows separation of concerns by providing a separate lifecycle for initialization related tasks
* **Ambassador**: Provide a unified interface for accessing services outside of the pod
* **Adapter**: Makes a system conform to a consistent unified interface that can be consumed easier

### Sidecar

![Sidecar](/digital-academy/images/structural-patterns/sidecar.png#center)

A Sidecar container extends and enhances the functionality of a pre-existing container without changing it. This is one of the fundamental container patterns that allows single purpose build containers to cooperate closely together for a greater outcome.

This simple pattern allows runtime collaboration of containers, and at the same time enables separation of concerns for both containers, which might be owned by separate teams using different programing languages, having different release cycles, etc. It also promotes replaceability and reuse of containers as node.js and git synchronizer can be reused in other applications and different configuration either as a single container in a pod, or again in collaboration with other containers.

### Initializers

![Initializers](/digital-academy/images/structural-patterns/initializers.png#center)

Kubernetes init containers feature allows separation of concerns by providing a separate lifecycle for initialization related tasks and the main application.

Init containers in Kubernetes are part of the pod definition, and they separate all containers in a pod in two groups: init containers and application containers. All init containers are executed in a sequence, one by one, and all of them have to terminate successfully before the application containers are started up. In that sense, init containers are like constructor instructions in a Java class which help for the object initialization. Application containers on the other hand run in parallel, and there is guarantee on the order in which they will start up.

And last but not least, init containers enable separation of concerns and allow keeping containers single purposed. An application container can be created by the application engineer and focus on the application logic only. An init container, can be authored by a deployment engineer and focus on configuration and initialization tasks only.

### Ambassador

![Sidecar](/digital-academy/images/structural-patterns/ambassador.png#center)

The Ambassador is a specialized Sidecar that it is responsible for hiding the complexity and providing a unified interface for accessing services outside of the pod. To demonstrate the pattern, the very first example that comes into mind is using a cache for an application. Accessing a local cache on the development environment may be a simple configuration, but on the production environment we may need a client configuration the is able to connect to the different shards of the cache.

Another example would be consuming a service that requires lookup in a registry and to service discovery on the client side. A third example would be consuming a service over a non-reliable protocol such as HTTP where to protect our application we have to use circuit breaker logic, configure time outs, perform retries, etc. In all of these of cases, we can use an Ambassador container that hides the complexity of accessing the external services and provides a simplified view and access to the main application container over localhost.

The benefits of this pattern are similar to those of Sidecar pattern where it allows keeping containers single purposed and reusable. With such a pattern, our application container can focus on its business logic and delegate the responsibility and specifics of consuming the external service to another specialized container. This also allows us the creation of specialized and reusable Ambassador containers that can be combined with other application containers.

### Adaptor

![Sidecar](/digital-academy/images/structural-patterns/adaptor.png#center)

The Adaptor pattern takes an diverse system and makes it conform to a consistent unified interface with standardise and normalized format that can be consumed by the outside world.

With this approach, every service represented by a pod, in addition to the main application container would have another container that knows how to read the custom application specific metrics and expose them in a generic format understandable by the monitoring tool. We could have an adapter container tha knows how to export Java based metrics over HTTP, another adapter container in a different pod that exposes Python based metrics over HTTP, etc. For the monitoring tool, all metrics would be available over HTTP, and in a common normalized format.

Another example would be logging. Different containers may log information in different format and level of details. An Adapter can normalize that, clean it up, enrich with some contextual information using SelfAwareness pattern and then make it available for scraping by the centralized log aggregator.

<!-- Links Referenced -->

[katacoda]:              https://katacoda.com
[katacoda-sylus]:        https://katacoda.com/sylus
[katacoda-containers]:   https://katacoda.com/sylus/courses/container-basics
