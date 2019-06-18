---
date: "2019-04-10T22:00:00-05:00"
title: "Kubernetes"
featureconfig:
  title: "Kubernetes"
  description: "A high level overview of Kubernetes."
  status: "Kubernetes"
  weight: 3
lastmod: "2019-04-10"
layout: single
linktitle: kubernetes
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
slug: fundamentals
toc: true
---

{{< youtube daVUONZqn88 >}}

## Introduction

> Kubernetes orchestrates the computing, networking, and storage infrastructure on behalf of user workloads.

**Kubernetes** is a portable, extensible open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a huge and rapidly growing ecosystem after being open sourced by Google in 2014, being the second largest repository on GitHub only behind Linux. It builds upon a decade and a half of experience that Google has with running production workloads at scale.

Kubernetes services, support, and tools are widely available as evidenced both by every single Cloud Service Provider now providing a managed service of it and big name companies putting their weight behind it in terms of both managed offerings and code contributions.

![KNative Architecture](/digital-academy/images/kubernetes/kubernetes-arch.png#center)

<div class="row">
  <div class="col-md-4">
    <h2 class="text-center">CSP</h2>
    <p class="text-center">Cloud Service Providers with managed service offerings</p>
    <ul class="mrgn-tp-lg">
      <li>Google (+++)</li>
      <li>Microsoft (++)</li>
      <li>AWS</li>
      <li>Digital Ocean</li>
      <li>IBM</li>
      <li>Oracle</li>
    </ul>
  </div>

  <div class="col-md-4">
    <h2 class="text-center">PaaS</h2>
    <p class="text-center">Platform as a service oferings of Kubernetes</p>
    <ul class="mrgn-tp-lg">
      <li>RedHat</li>
      <li>VMWare</li>
      <li>Pivotal</li>
    </ul>
  </div>

  <div class="col-md-4">
    <h2 class="text-center">Hyper Scale</h2>
    <p class="text-center">Powers much of the big name sites today</p>
    <ul class="mrgn-tp-lg">
      <li>Google</li>
      <li>Shopify</li>
      <li>GitHub</li>
      <li>Tinder</li>
      <li>Pokemon Go</li>
    </ul>
  </div>
</div>

## Background

As containers become more popular each day, more technology is being developed to help unleash all the power containers can offer. One of the most useful technologies created around containers in the deployment side is orchestrators.

A container orchestrator is a software that is able to orchestrate and organize containers across any number of physical or virtual nodes. This greatly simplifies the deployment of infrastructure (each node only has Docker installed) and its operation (as all servers are exactly equal). The container orchestrator takes into account things as failing nodes, adding more nodes to the cluster or removing nodes from the cluster by moving containers from one node to another to keep them available at all times.

While containers + orchestrators technology not only resolves a lot of problems related with infrastructure management, it also resolves a lot of problems regarding deploying software in the infrastructure, as the software, its dependencies and runtime are always deployed at the same time, minimizing the errors commonly associated to deployments in non-immutable environments.

## Highlights

1. **Managed complexity**: Automation of the deployment, scaling, and operation of application containers in a clustered environment via primitives

2. **Vendor Lock-in**: Essentially every major cloud provider (public, private, hybrid) support Kubernetes

3. **Multi-Cloud**: Ideal for managing multi-cloud environments / workloads

4. **Self-healing**: Auto-placement, auto-restart, auto-replication, auto-scaling

5. **Google Backed**: Originally built by Google leveraging a decade of experience running containers at scale

6. **Vibrant Community**: One of the fastest moving projects in open source history, 2nd largest. Additionally over 50,000 users on slack which include many google engineers.

7. **Big Name Supporters**: Google, Microsoft, AWS, IBM, RedHat, Oracle, VMWare, Intel, Pivotal etc

8. **Flexibility**: Simplicity of a PaaS with the flexibility of IaaS


<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-fundamentals]:    https://katacoda.com/sylus/courses/kubernetes-fundamentals-1
[katacoda-fundamentals-2]:  https://katacoda.com/sylus/courses/kubernetes-fundamentals-2
[katacoda-cncf]:            https://katacoda.com/sylus/courses/kubernetes-cncf
