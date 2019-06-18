---
date: "2019-04-16T22:00:00-05:00"
title: "Kubernetes CNCF"
featureconfig:
  title: "CNCF"
  description: "An overview of some of the more popular CNCF projects."
  status: "Kubernetes"
  weight: 10
lastmod: "2019-04-6"
layout: nested
linktitle: kubernetes
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
  - cncf
slug: cncf
toc: true
---

## Interactive Learning Environment

In this lab, we will examine the Docker CLI and execute our first container.

* [Kubernetes CNCF][katacoda-cncf]

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

The cloud native ecosystem can be complicated and confusing. Its myriad of open source projects are supported by the constant contributions of a vibrant and expansive community. The Cloud Native Computing Foundation (CNCF) has a landscape map that shows the full extent of cloud native solutions, many of which are under their umbrella.

## The History of the CNCF

In 2014 Google open sourced an internal project called Borg that they had been using to orchestrate containers. Not having a place to land the project, Google partnered with the Linux Foundation to create the Cloud Native Computing Foundation (CNCF), which would encourage the development and collaboration of Kubernetes and other cloud native solutions. Borg implementation was rewritten in Go, renamed to Kubernetes and donated as the incepting project. It became clear early on that Kubernetes was just the beginning and that a swarm of new projects would join the CNCF, extending the functionality of Kubernetes.

## The CNCF Mission

The CNCF fosters this landscape of open source projects by helping provide end-user communities with viable options for building cloud native applications. By encouraging projects to collaborate with each other, the CNCF hopes to enable fully-fledged technology stacks comprised solely of CNCF member projects. This is one way that organizations can own their destinies in the cloud.

## CNCF Processes

A total of twenty-five projects have followed Kubernetes and been adopted by the CNCF. In order to join, projects must be selected and then elected with a supermajority by the Technical Oversight Committee (TOC). The voting process is aided by a healthy community of TOC contributors, which are representatives from CNCF member companies, including myself. Member projects will join the Sandbox, Incubation, or Graduation phase depending on their level of code maturity.

Sandbox projects are in a very early stage and require significant code maturity and community involvement before being deployed in production. They are adopted because they offer unrealized potential. The CNCF’s guidelines state that the CNCF helps encourage the public visibility of sandbox projects and facilitate their alignment with existing projects. Sandbox projects receive minimal funding and marketing support from the CNCF and are subject to review and possible removal every twelve months.

Projects enters the Incubation when they meet all sandbox criteria as well as demonstrate certain growth and maturity characteristics. They must be in production usage by at least three companies, maintain healthy team that approves and accepts a healthy flow of contributions that include new features and code from the community.

Once Incubation projects have reached a tipping point in production use, they can be voted by the TOC to have reached Graduation phase. Graduated projects have to demonstrate thriving adoption rates and meet all Incubation criteria. They must also have committers from at least two organizations, have documented and structured governance processes, and meet the Linux Foundation Core Infrastructure Initiative’s Best Practices Badge. So far, only Kubernetes and Prometheus have graduated.

## CNCF Resources

* [Landscape][cncf-landscape]
* [Trail Map][cncf-trail]

The Cloud Native Landscape is intended as a map through the previously uncharted terrain of cloud native technologies. This attempts to categorize most of the projects and product offerings in the cloud native space. There are many routes to deploying a cloud native application, with CNCF Projects representing a particularly well-traveled path.

The Cloud Native trail map in addition provides a more constrained overview for enterprises and in our case departments wishing to start their cloud native journey.

> Note: While there are innumerable routes for deploying a cloud native application, CNCF Projects represent a particularly well-traveled, tested and trusted path.

<!-- Links Referenced -->

[cncf-landscape]:           https://github.com/cncf/landscape
[cncf-trail]:               https://github.com/cncf/trailmap
[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
[katacoda-fundamentals]:    https://katacoda.com/sylus/courses/kubernetes-fundamentals-1
[katacoda-fundamentals-2]:  https://katacoda.com/sylus/courses/kubernetes-fundamentals-2
[katacoda-cncf]:            https://katacoda.com/sylus/courses/kubernetes-cncf
