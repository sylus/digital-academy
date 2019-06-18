---
date: "2019-04-22T22:00:00-05:00"
title: "KNative"
lastmod: "2019-04-22"
layout: single
linktitle: knative
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - knative
slug: knative
toc: true
weight: 4
---

{{< youtube 69OfdJ5BIzs >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF: Module 6 - KNative][katacoda-knative]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Background

Event-Driven, sometimes also called serverless or functions as a service, is a computing execution model in which the 
infrastructure/provider dynamically manages the allocation of machine resources.

* Code is deployed to a CSP or elsewhere
* Instance does not exist until invoked, spins up and scales as required, scales to zero when complete
* Changes IT requirements related to security, administration (patching) and architecture

Currently there are various standards with little interoperability between Cloud Service Provider's. Common examples include the following:

* IBM OpenWhisk
* AWS Lambda
* Google Cloud Functions
* Azure Functions

## Overview

![KNative Architecture](/digital-academy/images/knative/knative.png#center)

**KNative** can be used to deploy serverless-style functions, applications, and containers to an auto-scaling runtime on Kubernetes. Additionally it has many other features:

* Deploy multiple versions
* Perform custom tasks on your app's source code
* Build reusable templates

<div class="row">
  <div class="col-md-4">
    <h2 class="text-center">Serving</h2>
    <p class="text-center">How your code recieves requests and scales with them</p>
    <ul class="mrgn-tp-lg">
      <li>Request-driven compute runtime</li>
      <li>Scale-to-zero / scale out per load</li>
      <li>Deploy from container registry</li>
      <li>Multiple revisions of same app</li>
      <li>Route traffic across revisions</li>
    </ul>
  </div>

  <div class="col-md-4">
    <h2 class="text-center">Build</h2>
    <p class="text-center">How your code is built and packaged as a container</p>
    <ul class="mrgn-tp-lg">
      <li>Pluggable model to build containers from source code</li>
      <li>Build in-cloud or on-cluster</li>
      <li>Push image to registry</li>
      <li>Templates available (buildpacks)</li>
    </ul>
  </div>

  <div class="col-md-4">
    <h2 class="text-center">Eventing</h2>
    <p class="text-center">How your code is triggered by events and executed</p>
    <ul class="mrgn-tp-lg">
      <li>Apps and functions consume and publish event streams</li>
      <li>Multiple event sources available</li>
      <li>Encourages asynchronous, loosely coupled architecture</li>
    </ul>
  </div>
</div>

> **Note**: Initially serverless, Functions as a service (FaaS) and event-driven architecture referred to a microservice that is run `on public cloud` compute engine only when invoked. Recently, serverless is also being used to describe managed container services (CaaS) and application deployments where the user is not responsible for the compute where/when the container runs (Azure AKS and ACI, AWS EKS and Fargate, Google GKE, Google App Engine, and IBM Kubernetes Service).

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-knative]:         https://katacoda.com/sylus/courses/kubernetes-cncf/module-6
