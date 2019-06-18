---
date: "2019-04-18T22:00:00-05:00"
title: "Introduction"
featureconfig:
  title: "Introduction"
  description: "A brief introduction of both the teachers and course format."
  status: "Introduction"
  weight: 0
lastmod: "2019-04-18"
layout: single
linktitle: introduction
authors: ["sylus"]
categories:
  - introduction
tags:
  - introduction
slug: introduction
toc: true
---

## Teachers

* [William Hearn][will]
* [Zachary Seguin][zachary]

## Cloud Native

A cloud native application is a distributed, elastic and horizontal scalable system composed of (micro)services which isolates state in a minimum of stateful components. The application and each self contained deployment unit of that application is designed according to cloud-focused design patterns and operated on a self service elastic platform. [^1]

> Note: Even though the specific tools and patterns may differ, cloud-native organizations and applications follow a fairly consistent pattern.

At its most fundamental level a cloud native application is engineered to run on a cloud platform and is designed for:

* **Resiliency**: Embraces failures instead of trying to prevent them; it takes advantage of the dynamic nature of running on a cloud platform
* **Agility**: Allows for fast deployments and quick iterations
* **Operability**: Adds control of application life cycles from inside the application instead of relying on external processes and monitors
* **Observability**: Provides information to answer questions about application state

## Cloud Native Infrastructure

Cloud native infrastructure is infrastructure that is hidden behind useful abstractions, controlled by APIs, managed by software, and has the purpose of running applications. Running infrastructure with these traits gives rise to a new pattern for managing that infrastructure in a scalable, efficient way.

Abstractions are useful when they successfully hide complexity for their consumer. They can enable more complex uses of the technology, but they also limit how the technology is used. They apply to low-level technology, such as how TCP abstracts IP, or higher levels, such as how VMs abstract physical servers. Abstractions should always allow the consumer to **“move up the stack”** and not reimplement the lower layers.

Cloud native infrastructure needs to abstract the underlying IaaS offerings to provide its own abstractions. The new layer is responsible for controlling the IaaS below it as well as exposing its own APIs to be controlled by a consumer.

Infrastructure that is managed by software is a key differentiator in the cloud. Software-controlled infrastructure enables infrastructure to scale, and it also plays a role in resiliency, provisioning, and maintainability. The software needs to be aware of the infrastructure’s abstractions and know how to take an abstract resource and implement it in consumable IaaS components accordingly.

## Open Source and Government

Government should build technology that uses open standards to ensure your system works and communicates with other products or systems, and can easily be upgraded and expanded.

Adopting and using **open standards** means you can:

* Move between different technologies when you need to, avoiding vendor lock-in
* Quickly and easily change your service when you need to
* Increase compatibility with all stakeholders
* Open up the range of companies you can purchase from as more of them are likely to use the same standard as you
* Access a wider range of both open source and proprietary software vendors

Our choices for hosting infrastructure, databases, software frameworks, programming languages and the rest of the technology stack should seek to avoid vendor lock-in and match what successful modern consumer and enterprise software companies would choose today. In particular, digital services teams should consider using open source software, cloud-based, and commodity solutions across the technology stack, because of their widespread adoption and support by successful consumer and enterprise technology companies in the private sector.

Open source software (OSS) tends to use and help define open standards and publicly available specifications. OSS products are, by their nature, publicly available specifications, and the availability of their source code promotes open, democratic debate around their specifications, making them both more robust and interoperable.

Using open source software means you can benefit from:

* Solving common problems with readily available open source technology
* More time and resource for customized solutions to solve the rare or unique problems
* Lower implementation and running costs


<!-- Links Referenced -->

[^1]:                       Kraztzke, N., Quint, P.C.: Understanding Cloud-Native Applications after 10 years of Cloud Compotuing - A Systematic Mapping Study. Journal of Systems and Software 126(April), 1-16 (2017)
[will]:                     https://sylus.ca
[zachary]:                  https://zacharyseguin.ca
[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
