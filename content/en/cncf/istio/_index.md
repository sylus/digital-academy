---
date: "2019-04-22T22:00:00-05:00"
title: "Istio"
lastmod: "2019-04-22"
layout: single
linktitle: istio
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - istio
slug: istio
toc: true
weight: 3
---

{{< youtube 1iyFq2VaL5Y >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF: Module 5 - Istio][katacoda-istio]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

When building microservice based application, a myriad of complexities arises, we need Service Discovery, Load balancing, Application resilience, Optimization of hardware utilization to name just a few. And as the application grows it gets progressively worse. Kubernetes providing a solution to these complexities was immediately well received and reached a wide adoption in all cloud computing service providers.

This simplification of Orchestrating Microservices enables teams to easily get to ten and then even more services, which causes a different set of problems, for example:

* **Traffic management**: Timeouts, retries, canary rollouts,
* **Security**: End-user Authentication and Authorization,
* **Observability**: Tracing, monitoring, and logging.

All of these can be solved and implemented in the application layer. But that’s an additional effort for the developer and a strain in the company’s resources. Resources that can and should be used to deliver business value to the customers. Istio take it away!

Istio is an Open Source project (developed in partnership between teams from Google, IBM, and Lyft) that solves all the above-mentioned problems, it is battle proven, as similar solutions have been used by these companies internally.

## Benefits

Istio provides an ingenious solution to all the above-mentioned requirements. The gist of Istio is to be completely separated from the services and act only by intercepting all network communication. And it turns out that by intercepting network communication it can implement:

* **Fault Tolerance** – Using response status codes know when a request failed and retry.
* **Canary Rollouts** – Forward only the specified percentage of requests to a service.
* **Monitoring and Metrics** – The time it took for a service to respond.
* **Tracing and Observability** – It adds special headers in every request and traces them in the cluster.
* **Security** – Extracts the JWT Token and Authenticates and Authorizes users.

{{< youtube oMf7jH1E3s0 >}}

## Architecture

Istio needs to intercept all the network communication to and from every service and apply a set of rules. This is achieved and logically split into two planes: `The Data Plane` and `The Control Plane`.


<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-istio]:         https://katacoda.com/sylus/courses/kubernetes-cncf/module-5
