---
date: "2019-04-16T22:00:00-05:00"
title: "Virtual Kubelet"
lastmod: "2019-04-16"
layout: single
linktitle: virtual-kubelet
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - virtual-kubelet
slug: virtual-kubelet
toc: true
weight: 3
---

{{< youtube lrb9XL6Pcas >}}

## Interactive Learning Environment

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Introduction

Virtual Kubelet is an open source Kubernetes kubelet implementation that masquerades as a kubelet for the purposes of connecting Kubernetes to other APIs. This allows the nodes to be backed by other services like ACI, AWS Fargate, IoT Edge etc. The primary scenario for VK is enabling the extension of the Kubernetes API into serverless container platforms.

Platforms:

* Azure Container Instances
* AWS Fargate
* Hyper.sh
* Hashicorp Nomad
* vShphere
* Etc

![Virtual Kubelet](/digital-academy/images/kubernetes/virtual-kubelet.png#center)

## Background

> Brendan Burns: The future will be containerized and those containers will run on serverless infrastructure

Thanks to wide container adoption among enterpise organizations a few things have become clear:

* Decoupling of the "layers" in the technology stack are producing a clean, principled layering of concepts with clear contracts, ownership and responsibility
* Introduction of these "layers" has enabled developers to focus their attention exclusively on the thing that matters to them namely **the application**.

This has happened before via the first generation of PaaS which focused on empowering developers to adopt "serverless" style architectures. While still incredibly useful for developer productivity there were often too many overlapping concepts mixed into a single monolith. A user who might want to leverage "serverless" but not the specific programming language or who wanted a more cost efficient model for large stateful applications might have some issues.

Thanks to the development of containers and the defacto decoupling of developer experience from serverless runtimes there is an huge growth this year of serverless container infrastructure and support among all major cloud providers as one can see by looking at the platforms supporting this above.

## Orchestration

Most if not all serverless container infrastructure like Azure Container Instances is raw infrastructure. Meaning it is an excellent way to easily run a few containers with zero issue(s). Building complicated systems, however, requires the development of an orchestrator to introduce higher level concepts like Services, Deployments, Secrets, etc. 

Kubernetes steps in for these serverless platforms, particularly, since the world is consolidating around the Kubernetes orchestration API, and the value of seamless integration with existing Kubernetes tooling is quite high thanks to tools like Istio and KNative.

## Future

Kubernetes was built to give developers a consistent and clean application oriented API that enabled them to forget about the explicit details of machines and machine management. Now thanks to the development of serverless container infrastructure this enables people to begin to forget about the machines entirely. However the successful use of serverless containers for larger scale applications requires the development of an orchestrator. This is why the integration of the Kubernetes orchestration layer and serverless container infrastructure is so crucial to the future success of both Kubernetes and the serverless infrastructure.

At this moment many experts are leveraging Kubernetes clusters that are a hybrid between dedicated machines and severless container infrastructure. The dedicated machines are often used for stateful services with relatively static / constant usage, as well as dedicated hardware like GPU's. Serverless containers are often used for burstable and / or stateless workloads.

## Example

A real world use case for Kubernetes and serverless container infrastructure has a ton of real-world uses cases for both batch and / or burstable workloads. For instance consider a department that is doing image processing across many pipelines. They can now rapidly spin up a large number of containers in quick sucession to handle a recent upload of images to shared storage. Within mere seconds they can go from no infrastructure, to hundreds of containers processing images, and when this processing is done, they immediately go back to paying nothing for the capacity. 

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-istio]:         https://katacoda.com/sylus/courses/kubernetes-cncf/module-5
