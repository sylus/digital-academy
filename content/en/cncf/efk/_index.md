---
date: "2019-04-22T22:00:00-05:00"
title: "EFK"
lastmod: "2019-04-22"
layout: single
linktitle: efk
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - efk
slug: efk
toc: true
weight: 3
---

{{< youtube QoHNkjtbHPg >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF: Module 4 - EFK][katacoda-efk]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Background

Any Kubernetes production environment will rely heavily on logs. Using built-in Kubernetes capabilities along with some additional data collection tools, you can easily automate log collection and aggregation for ongoing analysis of your Kubernetes clusters. We typically try to separate out platform logging from application logging. This may be done via very different tooling and applications, or even by filtering and tagging within the logs themselves.

As with any distributed system, logging provides the vital evidence for accurately tracing specific calls, even if they are on different microservices, so that a root cause of the issue(s) may be identified.

Here are our suggestions for logging within a distributed Kubernetes environment:

* Use a single, highly available log aggregator, and capture data from across the entire environment in a single place
* Create a single, common transaction ID across the entire end-to-end call for each client
* Ensure that service names and applications are being logged
* Standardize the logging levels within the entire stack
* Ensure that no data intended to be secure is being logged in the clear
 
## Logging in Kubernetes

Besides this high-level approach to logging, you should understand how Kubernetes handles its own logging and events.
Kubernetes nodes run on a virtual Linux computing platform. Components like kubelet and Docker runtime run natively on Linux, logging onto its local system. Linux logging is configured at different folder locations including the ubiquitous /var/log file.

The first thing an administrator should do is validate log rotations for these log files, as well as all the other miscellaneous Linux logs. Kubernetes’ documentation provides good recommendations for files to rotate. The logging configuration should be inspected even if you intend to replace the local logging mechanism with an alternative.

We don’t recommend that you keep logs for virtual compute instances inside ephemeral cloud computing environments. Such instances can disappear without notice. Modern logging and analytics tools provide enough context and visual aids to help operators determine what actually transpires inside large Kubernetes cluster deployments. You should use a log aggregation service to ship your logs away from the Kubernetes environment, for later review and analysis.

## Capturing Kubernetes Logs

For capturing Kubernetes and Application level logs we recommend using the Fluentd data collector to transport logs to an EFK stack. This log shipping method makes use of the command kubectl logs. You could have a logging pod with a Fluentd container on every node (Kubernetes makes this easy with the concept of DaemonSets). Fluentd will be configured (via configmap) to read all the log locations for every node and essentially aggregate them into a common searching location.

Another common variation on this approach where your application containers have a logging container in the same pod (A.K.A. Container Sidecar Approach), separating the application from the system logging.

> Note: The decoupling of the aggregation of logs away from the application code as this is very critical for both chain of responsibility and performrance.

## EFK Stack

Elasticsearch is an open source search engine known for its ease of use. Kibana is an open source Web UI that makes ElasticSearch user friendly for marketers, engineers, and data scientists alike.

* ElasticSearch
* Fluentd
* Kibana

By combining these three tools EFK (ElasticSearch, Fluentd, and Kibana) we get a scalable, flexible, and easy to use log collection and analytics pipeline.


<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-efk]:             https://katacoda.com/sylus/courses/kubernetes-cncf/module-4
