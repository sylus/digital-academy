---
date: "2019-04-22T22:00:00-05:00"
title: "Velero (Formerly Ark)"
lastmod: "2019-04-22"
layout: single
linktitle: velero
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
  - velero
slug: velero
toc: true
weight: 1
---

{{< youtube sHLRedoRSp0 >}}

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF: Module 2 - Heptio Velero][katacoda-velero]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

## Background

Heptio Velero is a utility for managing disaster recovery, specifically for your Kubernetes cluster resources and persistent volumes.

Velero gives you tools to back up and restore your Kubernetes cluster resources and persistent volumes. Velero lets you:

* Take backups of your cluster and restore in case of loss.
* Copy cluster resources across cloud providers. NOTE: Cloud volume migrations are not yet supported.
* Replicate your production environment for development and testing environments.

Velero consists of the following:

* A server that runs on your cluster
* A command-line client that runs locally

## Installation

The following is a rough example of how an installation looks like for a Kubernetes cluster when restoring snapshots in Azure.

```sh
AZURE_STORAGE_ACCOUNT_ID=velero
AZURE_STORAGE_KEY=<storage_key>
AZURE_TENANT_ID=<tenant_id>
AZURE_SUBSCRIPTION_ID=<subscription_id>
AZURE_RESOURCE_GROUP=management
AZURE_CLIENT_ID=<client_id>
AZURE_CLIENT_SECRET=<client_secret>

kubectl apply -f examples/common/00-prereqs.yaml

kubectl create secret generic cloud-credentials \
  --namespace heptio-ark \
  --from-literal AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID} \
  --from-literal AZURE_TENANT_ID=${AZURE_TENANT_ID} \
  --from-literal AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP} \
  --from-literal AZURE_CLIENT_ID=${AZURE_CLIENT_ID} \
  --from-literal AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET} \
  --from-literal AZURE_STORAGE_ACCOUNT_ID=${AZURE_STORAGE_ACCOUNT_ID} \
  --from-literal AZURE_STORAGE_KEY=${AZURE_STORAGE_KEY}

kubectl apply -f examples/azure/
```

## Velero Backup

Backup an application along with persistent volumes.

```sh
ark backup create traefik-backup --selector app=traefik \
                                 --snapshot-volumes \
                                 -n kube-system
```

Backup the Entire cluster with persistent volumes excluding the kube-system and heptio-ark namespaces.

```sh
ark backup create all --exclude-namespaces=kube-system,heptio-ark \
                      --snapshot-volumes
```

## Velero Schedule

Schedule a backup that will run on a cron schedule.

```sh
ark schedule create artifactory-backup --selector app=artifactory \
                                       --snapshot-volumes \
                                       --schedule "30 15 * * Sat"
```

## Velero Restore

Restore a backup along with persistent volumes.

```sh
ark restore create --from-backup artifactory-backup \
                   --restore-volumes
```

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-velero]:          https://katacoda.com/sylus/courses/kubernetes-cncf/module-2
