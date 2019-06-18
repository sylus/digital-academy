---
date: "2019-04-18T22:00:00-05:00"
title: "Statistics Canada"
featureconfig:
  title: "Statistics Canada"
  description: "A brief introduction to some of the work being done at Statistics Canada."
  status: "Statistics Canada"
  weight: 1
lastmod: "2019-04-18"
layout: single
linktitle: statcan
authors: ["sylus"]
categories:
  - statcan
tags:
  - statcan
slug: statcan
toc: true
---

{{< youtube rtELspC-TXM >}}

## Introduction

Currently at Statistics Canada we manage a number of Kubernetes clusters of varying size leveraging multiple types of workloads in our primary cloud service provider Azure. Right now we use `AKS-Engine` which is slightly different then AKS though is the open source component of it and its upstream in terms of its codebase.

## Overview

Minimum set of Kubernetes clusters:

* Innovation
* DevTest
* QA
* Production
* Management

All of our deployed clusters meet the following criteria:

* **Enforce RBAC**: All users are associated to a namespace leveraging Active Directory for authentication
* **Policy Enforcement**: All applications deployed into it are aligned with departmental security standards
* **Security**: All communications are encrypted at rest and in transit. Additionally everything is behind fortigate.
* **Network Policy**: Applications by default have a high constraint network policy by which can talk to other network endpoints
* **Automated CI/CD**: Deployment of applications are done through a vetted CI/CD process
* **Automated backups**: Backups of both applications and cluster state made simple via selectors
* **Infrastructure as Code**: All of our clusters can be destroyed and recreated entirely through automation.
* **Hybrid Workloads**: Support both Linux and Windows workloads

{{< youtube Nj6dqU7u0CI >}}

## Clusters

In addition to all of the StatCan developped applications that we're migrating to Kubernetes, we also run our supporting services in Kubernetes:

* Artifactory / XRay
* Ansible AWX
* Cachet
* Cloud Financial Reporting
* Grafana
* GitLab
* Jenkins
* Octopus
* Prometheus
* Sentry

CI Runners:

* Jenkins Runner
* GitLab Runner

We're also evaluating running the following on Kubernetes:

* Confluence
* JetBrains License Server
* JIRA
* JupyterHub
* Kibana
* Minio
* Pachyderm
* Radar
* Rundeck

We are actively working with other Statistics Canada teams to migrate/develop applications to run on Kubernetes.

{{< youtube Ct6ErJAOV1Q >}}

<!-- Links Referenced -->

[will]:                     https://sylus.ca
[zachary]:                  https://zacharyseguin.ca
[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
