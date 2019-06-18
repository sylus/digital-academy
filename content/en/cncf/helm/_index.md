---
date: "2019-04-122T22:00:00-05:00"
title: "Helm"
lastmod: "2019-04-22"
layout: single
linktitle: helm
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - helm
slug: helm
toc: true
weight: 0
---

{{< youtube yslaP0r11Dg >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF: Module 1 - Helm][katacoda-helm]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

Helm is the official package manager for Kubernetes.

Helm Charts helps you define, install, and upgrade even the most complex Kubernetes application with ease.

Think of Helm as the package manager for Kubernetes similar to apt / yum / apk. If you deploy applications to Kubernetes, Helm makes it incredibly easy to version those deployments, package it, make a release of it, and deploy, delete, upgrade and even rollback those deployments as charts. Charts being the terminology that helm use for package of configured Kubernetes resources.

The latest version of Helm is maintained by the CNCF - in collaboration with Microsoft, Google, Bitnami and the Helm contributor community.

### Key Benefits

* Official Kubernetes package manager
* Reproducible builds of your k8s applications
* Intelligently manage your k8s manifest files
* Manage releases of Helm packages
* Subchart capability

### Why Teams Love Helm

* **Manage Complexity**: Charts describe even the most complex apps; provide repeatable application installation, and serve as the sole single point of authority.
* **Easy Updates**: Take the pain out of updates with in-place upgrades and custom hooks.
* **Simple Sharing**: Charts are incredibly easy to version, share, and host on public and/or private servers.
* **Rollbacks**: Use helm rollback to roll back to an older version of a release with ease.
* **Workflow**: Helm alongside with related tools such as Draft, Brigade, and Kashti offer a rich workflow for application delivery.

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-helm]:            https://katacoda.com/sylus/courses/kubernetes-cncf/module-1
