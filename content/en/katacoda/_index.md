---
date: "2019-04-15T22:00:00-04:00"
title: "Katacoda Interactive Learning Environment"
featureconfig:
  title: "Katacoda Interactive Learning Environment"
  description: "Katacoda is a platform that enables you to run Docker and Kubernetes in your web browser."
  status: "Katacoda"
  weight: 1
lastmod: "2019-04-15"
layout: single
linktitle: containers
authors: ["sylus"]
categories:
  - katacoda
tags:
  - katacoda
slug: katacoda
toc: true
---

{{< youtube KeJJ34BvA7Q >}}

## Interactive Learning Environment

For our labs, we utilize Katacoda. Katacoda is a platform that enables you to run Docker and Kubernetes in your web browser. Each lab has multiple steps where we'll explain concepts and how you can utilize them in Kubernetes.

> All of our labs are located at this [Profile][katacoda-sylus].

The Katacoda environment is a Linux distribution where we can run for the most part whatever we want. Katacoda simplifies the teaching process by allowing us to click on commands to execute them. For example:

`ls{{execute}}`

When you can click on the above, it will then execute `ls` inside your environment.

Most labs that we run will have multiple hosts. Your current host is the master node that operates the cluster. You also have another host that is a worker node, which runs workloads.

For consistency we always keep files needed for our labs in the `./resources/` folder.

> Note: When we go through the lab we the teachers will usually be executing the command first and then asking the class to follow suit afterwards.

<!-- Links Referenced -->

[katacoda]:              https://katacoda.com
[katacoda-sylus]:        https://katacoda.com/sylus
