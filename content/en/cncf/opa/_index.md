---
date: "2019-04-23T22:00:00-05:00"
title: "Open Policy Agent"
description: "Policy-based control for cloud native environments"
lastmod: "2019-04-23"
layout: single
linktitle: opa
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - opa
slug: opa
toc: true
weight: 2
---

{{< youtube nzcnf83NvxE >}}

## Interactive Learning Environment

In this lab, we will learn about the Open Policy Agent and how to set policies.

* [Kubernetes CNCF: Module 7 - Open Policy Agent][katacoda-opa]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Background

Open Policy Agent (OPA) is a general-purpose policy engine with uses ranging from authorization and admission control to data filtering. OPA provides greater flexibility and expressiveness than hard-coded service logic or ad-hoc domain-specific languages. And it comes with powerful tooling to help you get started. You can integrate OPA as a sidecar, host-level daemon, or library.

Here are just a few examples of what you can do with OPA:

* Kubernetes Admission Control
* HTTP API Authorization
* Remote Access
* Data Filtering with Partial Evaluation

> Services offload policy decisions to OPA by executing queries. OPA evaluates policies and data to produce query results (which are sent back to the client). Policies are written in a high-level declarative language and can be loaded into OPA via the filesystem or well-defined APIs.

## Why use OPA?

![DevOps Pipeline](/digital-academy/images/opa/benefits.svg#center)

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-opa]:             https://katacoda.com/sylus/courses/kubernetes-cncf/module-7
