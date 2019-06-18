---
date: "2019-04-18T22:00:00-05:00"
title: "Kubernetes Security"
featureconfig:
  title: "Kubernetes Security"
  description: "Best practices for securing Kubernetes."
  status: "Kubernetes"
  weight: 6
lastmod: "2019-04-18"
layout: single
linktitle: kubernetes-security
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
slug: security
toc: true
---

## Interactive Learning Environment

> All of our labs are powered by [Katacoda][katacoda] and are located at this [Profile][katacoda-sylus].

{{< figure src="/digital-academy/images/kubernetes/kubernetes-security.png#center" class="text-center" title="Figure 1: Security" >}}

As Kubernetes is entirely API driven, controlling and limiting who can access the cluster and what actions they are allowed to perform is the first line of defense. The following is a meant as a high level guide for what should be done at a minimum in a Kubernetes cluster to ensure that it is actively secured and guarded against any malicious intrusion.

## Logging and Auditing

All logs from the cluster are sent to a central logging service for rentention and analysis. These logs include:

* Kubernetes control plane
* Pod logs
* Node logs

On Azure, this is accomplished with the OMS agent plugin which sends logs to Log Analytics. An open source alternative is to run flutend and send refined logs to Elasticsearch which is what we currently recommend.

## User authentication and authorization

Kubernetes offers a variety of authentication strategies including: client certificates, OpenID Connect Tokens, Webhook Token Authentication, Authentication Proxy, Service Account Tokens, and several more. Each strategy has its benefits and drawbacks but ultimately, they are all responsible for asserting the identity of the user making an API call so that the Kubernetes RBAC framework can then decide if the caller is authorized to perform the requested action.

### Role-based access control

Kubernetes clusters are deployed with role-based access control (RBAC). Within Kubernetes, RBAC restricts the permissions of users and services to those explicitly allowed. Kubernetes RBAC is configured via policies applied within the cluster.

### OpenID Connect

Kubernetes supports external authentication with providers supporting OpenID Connect. In Azure, using AKS Engine, this integration can be added during cluster creation by following the documentation.

### User/Group permissions

By default, users and groups have no permissions within the cluster. For security reasons, users and groups should be given the minimum privileges
required. Additionally, permissions given to users should be restricted to specific namespaces rather than cluster wide.
See Roles and Role Bindings below for documentation on how to assign users and groups permissions.

### Service accounts

Applications within the cluster requiring access to the Kubernetes API should have their own service accounts. These service accounts can be given the required permissions via roles and role bindings.

### Roles and role bindings

There are two types of roles within a cluster:

* Roles
* ClusterRoles

Similarily, there are two types of bindings:

* RoleBindings
* ClusterRoleBindings

#### Roles

Role objects apply to the namespace in which they are created. Roles are assigned to users, groups and service principals via RoleBinding objects.
Again, role bindings apply only the namespace in which they are created.

An example role and role binding:

{{< highlight yaml >}}
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
name: limited-user
rules:
- apiGroups:
- ""
- apps
- extensions
resources:
- deployments
- cronjobs
- jobs
- secrets
- services
- persistentvolumeclaims
- pods
- pods/attach
- pods/exec
- pods/log
- configmaps
- ingresses
verbs:
- get
- list
- watch
- create
- update
- patch
- delete
- edit
- exec
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
name: $USER-limited-user
subjects:
- kind: User # User, Group or ServiceAccount
name: https://sts.windows.net/<tenant-id>/#<user-id>
namespace: default # Only needed for ServiceAccount
roleRef:
kind: Role
name: limited-user
apiGroup: rbac.authorization.k8s.io
{{< /highlight >}}

> Note: When using Azure Active Directory, users are referenced via https://sts.windows.net/<tenant-id>/#<user-id>. Groups are references only by their object id. See documentation for more information.

#### Cluster Roles

ClusterRole objects are cluster-wide (i.e, they are not namespace specific). Users, groups and service principals are given these roles via ClusterRol
eBinding objects.

{{< highlight yaml >}}
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
name: limited-user
rules:
- apiGroups: [""]
resources:
- nodes
- namespaces
verbs:
- get
- list
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
name: $USER-limited-user
subjects:
- kind: User
name: https://sts.windows.net/<tenant-id>/#<user-id>
namespace: default
roleRef:
kind: ClusterRole
name: limited-user
apiGroup: rbac.authorization.k8s.io
{{< /highlight >}}

> Note: The use of cluster roles is discouraged. However, they are necessary for granting certain permissions: for example, listing namespace. These permissions should only be given with an appropriate review of the security risks.

## Resource Quotas

To prevent abuse (either accidental or purposeful), quotas should be set for the different Kubernetes objects.

At minimum, namespaces should have the following quotas applied:

Quota                  | Value | Notes
---------------------- | ----- | --------------------------------------------
pods                   |       | A misconfiguration of jobs, deployments, etc. can result in the creation of many pods
services.loadbalancers |   0   | Prevent users for exposing applications via load balancers. Web applications (aka. HTTP) should be exposed via a ingress.
services.nodeports     |   0   | See above

> Note: A list of available quotas is available in the Kubernetes Resource Quotas documentation.

### Pod Resource Requests

Pods should request appropriate resources (CPU and memory). All pods must have a resource limit (CPU and memory) applied. This allows the scheduler to appropriately schedule pods based on available resources and to prevent a single application from consuming all available resources on a node.

## Networking Security

Network security consists of the policies and practices adopted to prevent and monitor unauthorized access, misuse, modification, or denial of a computer network and network-accessible resources. Network security involves the authorization of access to data in a network, which is controlled by the network administrator.

### Network Policies

Clusters use a Container Network Interfaces (CNI) plugin which supports network policies.

Within Azure, clusters use the Azure CNI plugin. Azure CNI supports network policies for Linux containers. At this time, no Open Source CNI plugin supports Windows network policies.

Network policies operate at layer 4. Rules can be defined based on:

* Source IP/Protocol/Port
* Destination IP/Protocol/Port

> Note: Since all Kubernetes objects are dynamic, the network policies are able to reference them via labels rather than knowing their source/destination IPs. The rules will automatically update as pods come and go.

### Service Mesh

Service meshes are designed to operate at Layer 7 (where possible). This allows them to:

* Route based on information within the request (source, headers, method, path, etc.). In addition to selecting the appropriate backend, this allows a/b service testing, add more, etc.
* Restrict access to services, or parts of services (e.g., permitted HTTP methods)
* Set requests timeouts
* Automatically retry requests upon failure

#### Security

Service meshes have to ability to restrict outgoing network connections. External services can be individual whitelisted as required.

Service meshes track all connections, and where possible, the information contained within the request. This information can be centrally logged for later
review.

Working in conjunction with network policies, we can force all traffic through the mesh preventing bypass vulnerabilities.

#### Mutual TLS

Most service meshes support mutual TLS authentication. This provides:

* Assurance as to who is sending the request
* Assurance that you are talking to who you are expecting to be talking to
* Communication is encrypted between to client and the server

Effectively, this provides end-to-end TLS for services within the Kubernetes cluster.

#### Observability

Because all connections through the service mesh are tracked, we get visibility as to what pods are connecting to. This includes services within the cluster
and those outside of the cluster.

The tracking of requests includes:

* Source and destination
* Request information (if sent plain text)
* Response status

### Private Network Space

To limit exposure, Kubernetes, and in particular, the API server, should not be exposed to the internet.

## Application security

Every organization has some rules. Some of these are essential to meet governance, and legal requirements and other are based on learning from past experience and not repeating the same mistakes. These decisions cannot tolerate human response time as they need near a real-time action. Services that are policy enabled to make the organization agile and are essential for long-term success as they are more adaptable as violations and conflicts can be discovered consistently as they are not prone to human error.

Kubernetes compliance is enforced at the “runtime” via tools such as network policy and pod security policy. kubernetes-policy-controller extends the compliance enforcement at “create” event not at “run“ event. For example, a kubernetes service could answer questions like :

* Can we whitelist / blacklist registries
* Not allow conflicting hosts for ingresses
* Label objects based on a user from a department
* What are the policies that my cluster is violating

### Policy Controller

* Restrict image sources. For example, restrict to pulling only from artifactory.cloud.statcan.ca
* Ensure ingress matches the Kubernetes cluster expected host name
* Require annotation to all objects defining contact for the application
* Automatically ensuring that windows annotation is used for matching workload
* Namespace PE would have annotation and auto apply to nested resources for costing
* Ensure that any load balancers made is automatically made into an internal one

<!-- Links Referenced -->

[katacoda]:                 https://katacoda.com
[katacoda-sylus]:           https://katacoda.com/sylus
