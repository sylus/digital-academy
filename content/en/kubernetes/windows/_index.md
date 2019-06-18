---
date: "2019-04-04T22:00:00-04:00"
title: "Deploying Windows Apps with Kubernetes"
featureconfig:
  description: "Orchestrating Windows Containers with Kubernetes"
  status: "Kubernetes"
  title: "Windows with Kubernetes"
  weight: 8
lastmod: "2019-04-04"
layout: single
linktitle: Windows
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - windows
  - containers
slug: windows
toc: true
---

## Windows and Kubernetes

Windows has now reached production level support for Windows nodes as of Kubernetes v1.14. Due to this we can support various types of Windows workloads in a container and consequently to run in Kubernetes cluster. During the course of this presentation, we'll go over some of the key considerations such as handling secrets, required node selectors, and other common differences you may encounter in clusters with Windows & Linux nodes.

Once the fundamentals are in place we can then move on to setting up the full developer workflow where we will go over how to create a new app using both .NET Framework (WCF, Console, MVC) and .NET Core using the various tools at our disposal.

This includes a discussion of the following tools:

* [Draft][draft]
* [Helm][helm]
* [Jenkins][jenkins]
* [Octopus][octopus]
* [Artifactory][artifactory] / [XRay][xray]

## Roadmap

There has been significant work on getting Kubernetes to support Windows applications and much of the heavy lifting has been done by Microsoft. This is why Azure is the only CSP at the moment to support Windows workloads in Kubernetes.

 2016-2017                                                              | 2017-2018                                                                             |  2018-2019
----------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | --------------------------------------------
 Containers in Windows Server (preview)                                 | Parity with core k8s features (configmap, pvc, dns resolution)                        | Windows Server 2019 / 1809
 SIG-Windows formed (Apprenda, TicketMaster, Docker, Huawei, etc)       | Networking (CNI) and storage improved in k8s 1.6 - 1.10                               | Product preview coming (AKS, Docker, RedHat)
 Kubernetes 1.5 alpha using Windows Server 2016                         | Windows Server 1709 / 1803 (number of containers + patch)                             | Testing, docs for v1.13+ and stable for v1.14

> Note: Windows Server 1709 / 1803 currently need patches to address issues with larger numbers of containers

## Building Windows Containers

* Nuget Restore -> Build solution -> Image build -> Package -> Deploy
* Common Core / Proper Dependency Handling
* Single repo for separate functionality

### Kernel / User compatibility

* Windows kernel major version needs to match
* If built on Windows Server 2019 then must run on same version
* Hyper-V isolation can run older containers on a newer node

### NuGet

* NuSpec -> NuGet -> Package.config
* Encrypted Key
* Common Core DLL's pulled in
* Don't store dependencies, only source code

The NuGet.config file should have the following added to it if leveraging Artifactory:

```sh
<add key="ArtifactoryNuGetV3" value="https://<domain>/artifactory/api/nuget/v3/nuget" protocolVersion="3" />
```

Then to authenticate against Artifactory with the NuGet Encrypted API key, run the following command:

```sh
nuget setapikey <USERNAME>:<PASSWORD> -Source ArtifactoryNuGetV3
```

To configure the NuGet Visual Studio Extension to use Artifactory, you need to add this repository as another Package Source under Nuget Package Manager.

`Tools > NuGet Package Manager > Settings > Package Sources`

1. Go to the "Package Manager Settings"
2. Add a custom name for the package source
3. Follow the steps mentioned above

### *.csproj

Depending on the project type and architecture you might need to modify the *.csproj file.

* Support all types of builds (CLI, Visual Studio, Docker)
* In-place building + publishing profile
* No relative includes
* Only use assembly references needed

### Configuration

* Environment variables passed at runtime
* Proper overrides of web.config and related configuration
* Leverage KeyVault for connection strings, etc (easily change environments)

Where possible you should never bake your configuration settings inside your docker container. A much better approach is to supply the transformation at the time of container startup.

One of the biggest benefits of using containers is the ability to wrap your application and its dependencies into an immutable image. This image is built once and deployed to different environments as it progresses through a continuous delivery pipeline.

As the application is deployed, we can then pass variables or mount configuration files into the container to configure it for the given environment. This ensures that we deploy the exact same application to production as the one tested in other environments. This approach also helps keep secrets out of container images.

* [Legacy Support][legacy-support]
* [Configuration Builder][config-builder]

### Dockerfile

* Multi-stage build (keep final image small)
* Types of base images (sdk, runtime)
* Handling IIS webservers
* MSBuild Parameters and Publish Profiles
  * Difference between msbuild and dotnet (cli)
  * 4.6.1+ needs msbuild because dotnet doesn't compile
* Dockerignore (ensure Docker does all the building)

```sh
bin
obj
packages
Dockerfile
Jenkinsfile
```

There are a variety of supported Windows environments:

* Windows Server 2016
* Windows Server 1803
* Windows Server 1809
* Windows Server 2019

For each of the Windows environments you have the supported targets:

* dotnet (microsoft/dotnet-framework:4.7.2-runtime)
* aspnet (microsoft/aspnet:4.7.2 AS runtime)
* wcf (microsoft/wcf:4.7.2 AS runtime)

The following is an example of a full Dockerfile for working with WCF.

#### WCF

{{< highlight dockerfile >}}
# escape=`

# WCF Server 1803
FROM <domain>:443/docker-remote/microsoft/dotnet-framework:4.7.2-sdk AS build
WORKDIR /app

# Build and package dotnet app
COPY . .
RUN nuget restore
RUN msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile="FolderProfile.pubxml"

# Multi stage build
FROM <domain>:443/docker-remote/microsoft/wcf:4.7.2-windowsservercore-1803 as runtime

RUN MKDIR AuditLog
WORKDIR AuditLog
COPY --from=build /app/bin/Release/Publish ./

# Configuration of the new site in IIS
RUN powershell -NoProfile -Command `
    Import-module IISAdministration; `
    New-IISSite -Name "AuditLog" -PhysicalPath C:\Scripts\AuditLog -BindingInformation "*:83:"

EXPOSE 83
{{< /highlight >}}


## Building Hybrid Clusters

* Apps across multiple nodes + operating systems
* Same workflow regardless of node (api, kubectl, helm)
* Manage Linux from Windows, and Windows from Linux
* Same supporting container infrastructure (container registries)

There are currently two ways to leverage Windows workloads in Kubernetes on Azure. For the purposes of this presentation we will only be talking about how to use AKS Engine.

1. Leverage AKS-Engine
2. Manually add a Windows virtual machine to the cluster

> Note: Managed AKS with Windows support should be general availability by the end of March 2019.

### AKS Engine Cluster Definition

The following snippet is the initial cluster definition we pass to AKS Engine in order for it to generate the complete ARM deployments along with the configuration that Kubernetes expects.

{{< highlight json >}}
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "orchestratorRelease": "1.12",
      "kubernetesConfig": {
        "useManagedIdentity": true,
        "userAssignedID": "k8s-prod-msi",
        "enableDataEncryptionAtRest": true,
        "enableEncryptionWithExternalKms": true,
        "enableRbac": true,
        "privateCluster": {
          "enabled": true
        },
        "networkPolicy": "azure",
        "enableRbac": true,
        "kubeletConfig": {
          "--allow-privileged": "true",
          "--anonymous-auth": "false",
          "--max-pods": "60",
          "--network-plugin": "cni"
        },
        "gcHighThreshold":85,
        "gcLowThreshold": 80,
        "apiServerConfig": {
          "--allow-privileged": "true",
          "--anonymous-auth": "false",
        },
        "addons": [
          {
            "name": "tiller",
            "enabled": true
          },
          {
            "name": "kubernetes-dashboard",
            "enabled": true
          },
          {
            "name": "container-monitoring",
            "enabled": true
          },
          {
            "name": "rescheduler",
            "enabled" : true
          }
        ]
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "k8s-aks-production",
      "vmSize": "Standard_D4s_v3",
      "OSDiskSizeGB": 200,
      "vnetSubnetId": "/subscriptions/<subscription_id>/resourceGroups/<network_rg>/providers/Microsoft.Network/virtualNetworks/<vnet>/subnets/Container-Rz-Master",
      "firstConsecutiveStaticIP": "XXX.XX.X.X",
      "vnetCidr": "XXX.XX.X.X/24"
    },
    "agentPoolProfiles": [
      {
        "name": "linuxpool1",
        "count": 2,
        "customNodeLabels": {
          "os": "linux"
        },
        "vmSize": "Standard_D8s_v3",
        "OSDiskSizeGB": 200,
        "storageProfile" : "ManagedDisks",
        "availabilityProfile": "AvailabilitySet",
        "vnetSubnetId": "/subscriptions/<subscription_id>/resourceGroups/<network_rg>/providers/Microsoft.Network/virtualNetworks/<vnet>/subnets/<subnet>"
      },
      {
        "name": "windowspool1",
        "count": 1,
        "customNodeLabels": {
          "os": "windows"
        },
        "osType": "Windows",
        "vmSize": "Standard_D8s_v3",
        "OSDiskSizeGB": 200,
        "storageProfile" : "ManagedDisks",
        "availabilityProfile": "AvailabilitySet",
        "vnetSubnetId": "/subscriptions/<subscription_id>/resourceGroups/<network_rg>/providers/Microsoft.Network/virtualNetworks/<vnet>/subnets/<subnet>"
      }
    ],
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "ssh-rsa ..."
          }
        ]
      }
    },
    "windowsProfile": {
      "adminUsername": "azureuser",
      "adminPassword": "<pass>"
    },
    "servicePrincipalProfile": {
      "clientId": "<client_id>",
      "secret": "<secret>",
      "objectId": "<object_id>"
    }
  }
}
{{< /highlight >}}

> Note: Remember to make sure that the Windows version you launch with is the same version you want to build and run your containers in.

## Orchestration

* Helm Chart
  * How to write / generate a Helm Chart
  * Showcase a few examples of what we wrote

With our Windows Containers built and a Hybrid Kubernetes cluster instantiated we can now discuss how they can be successfully orchestrated in a distributed fashion.

### Considerations

* Need to consider where container runs if need a windows server node use node selector
* Option 1: Make use of taints and tolerations for Windows nodes
* Option 2: Update helm charts and yaml files
* Resource consumption: Need higher limits (300MB) as need windows background services
* AKS Engine: Issue with them where do they go, and check GitHub

## Release Automation

![DevOps Pipeline](/digital-academy/images/windows/cicd.png#center)

* Draft: Generate your skeleton
* Helm: Package manager
* Jenkins: Build process and push to container registry
  * How to write Jenkinsfile to support Windows + Linux
  * JNLP for Windows + Linux and how to write
  * Kubernetes Plugin (latest version)
* Octopus: Continuous deployment with business processes baked in
* Artifactory / XRay: Security scanning for containers against all known CVE's
* Securing cluster(s)

### Jenkins

We have built our own containerized Jenkins JNLP agent against Windows 1803 and using the `dotnet-framework` base image.

Additionally leveraging chocolatey we have added a few other commonly required libraries.

Repository: [windows-jnlp-agent-windows][windows-jnlp-agent-windows]

#### Jenkinsfile

The following is largely a fully complete `Jenkinsfile` tailored to work with Windows nodes inside a Kubernetes cluster.

Simply replace the values below with your own configuration.

> Note: We makes use of the Kubernetes plugin where you can specify the specific cluster you wish to run CI tests against.


{{< highlight groovy >}}
def label = "jnlp-win-${UUID.randomUUID().toString()}"

podTemplate(
  cloud: '<k8s-cluster>',
  serviceAccount: 'jenkins-jenkins
  label: label,
  yaml: """
apiVersion: v1
kind: Pod
metadata:
  labels:
    ci: windows-vs2017
spec:
  containers:
    - name: jnlp
      image: <docker-registry>/jenkins/jenkins-ext:jnlp-windows
      env:
        - name: JENKINS_URL
          value: 'https://<domain>'
        - name: DOCKER_HOST
          value: 'tcp://<ipaddress>:2375'
      tty: true
      working: 'C:\\workspace\\'
  tolerations:
    - effect: NoSchedule
      key: os
      operator: Equal
      value: windows
  nodeSelector:
    agentpool: ciwinpool1
"""
) {
    node (label) {
      // Update the gitlab status to pending
      // https://jenkins.io/doc/pipeline/steps/gitlab-plugin
      updateGitlabCommitStatus name: 'build', state: 'pending'

      bat 'powershell.exe mkdir C:\\repo'
        dir('C:\\repo\\') {
            checkout scm

            withCredentials([usernamePassword(credentialsId: 'docker-pull', passwordVariable: 'pass', usernameVariable: 'user')]) {
                bat "docker login -u ${user} -p ${pass} <docker-registry>"
                bat "docker build . -t <docker-registry>/<repo-name> --no-cache"
                bat "docker push <docker-registry>/<repo-name>"
            }
        }
      bat 'powershell.exe remove-item C:\\repo -recurse -force'

      // Update the gitlab status to success
      updateGitlabCommitStatus name: 'build', state: 'success'
    }
}
{{< /highlight >}}


## Developer Perspective

* Learn new scripting
* Learn groovy, powershell, azure cli
* Learn Docker + Visual Studio integration with docker
* Limit betwen DevOps + Docker and where the overlap is
* JenkinsFile or Similar CI
* Prepared to learn and read resources (we can provide)
* When should we go to .NET core
  * Performance
  * Speed of delivery
  * Density of Node
* 3 types of Applications
  * WCF
  * Console
  * ASP.Net Web Application (should evaluate for this)
* Showstoppers
  * Related to Oracle (management DLL forget about .NET Core)
  * Why don't move to managed, we are using UDT only support for this
* Once .NET core is achieved
  * Is way less complex so it is encourage
  * For new projects should try to do .NET core (simplicity and stability of CI)
  * Our project PMDAC was successful for this (web.config improvements)
  * For existing project should access this, as runs better in Linux
  * Densely pack the node more and build time is dramatically reduced
  * Integration with Visual Studio
    * Debug container directly in IDE, breakpoint
    * Tested with .NET Core
    * First class integration in VSCode / Visual Studio
* Building application with Dockerfile
  * If don't have automation process for deployment then use visual studio to generate
  * We are doing it all in Dockerfile to keep all deps together
  * Need to understand how to use it in CLI / multi staging
  * Branching strategy, Layering
  * Dependency on Windows 10, Docker for Windows (mandatory) (important)
* Agile Practies
  * Useful to have CI first
  * When starting to fix issues, we can fail fast, fail often to succeed
  * CI will reduce time for developers later on
  * We use Jenkins -> GitLab
* Separation of repositories
  * Helps to focus on task at hand
  * Faciliates a micro-services oriented approach
  * Challenge to refactor that
* DevOps and Developer perspective
* Ask them which people are coming and there background so we can tailor discussion
* Single Page application / Razor / Angular etc
* Traefik (https)

### Anti-Patterns

Right now it is unavoidable that the developer needs to learn a bit about Docker / Kubernetes in order to successfully deploy a Windows application on Kubernetes.

However this can technically be classified as a anti-pattern as we should strive for the developer workflow to remain consistent and for the orchestration to be seamless without any developer intervention. This is one of the main goals of the KNative initiative which brings serverless functionality to Kubernetes.

## Debugging

There a quite a few ways to debug Windows Containers and / or your Windows Node on Kubernetes. Additionally the problem scope differs based on the level of the stack where you are experience an issue.

Always important to remember to start small and try to isolate the issue.

Here is some useful information that might help you further troubleshoot.

## Debugging Windows Container with Docker Compose

Even though docker swarm is on the way out and being wholly replaced by Kubernetes, docker-compose itself on a single virtual machine can prove very useful in order to prove things work before deploying your applications into a distributed cluster.

This is very useful for debugging as sometimes don't know where specific issues are in the cluster.

* Network Connectivity / Latency as everything is local
* Issues with Service Endpoints / Timeouts
* Memory / CPU issues
* Number of Windows Containers

Another important fact is that if everything works in a local docker-compose environment, it is a safe bet your containers should also work in a Kubernetes cluster with certain caveats applied.

## Debugging Windows Nodes

* How to debug Windows Containers
* SSH in to Windows Node
* How to read kubelet logs / journalctl
* Wireshark
* How to know which layer (Traefik)
* Often use a known working trivial example to test communication between services
  * [Ping Service][pingsvc]
  * [Pong Service][pongsvc]
* Known issues and how to patch Windows Nodes

## Help

There are quite a few places where you can get help with issues related to Windows and Kubernetes. We have had particular success in the Slack channel and GitHub issue queue when their was spefic problems related to our Windows node.

* [Slack][slack] (#sig-windows / #sig-azure)
* [GC Message][gcmessage]
* [GitHub][aks-engine]
* Azure Premium Ticket

> Note: Please remember to be concise with your issue and always reference your Kubernetes version, Windows Kernel version, and specific network configuration.

## Referenced Links (In order they appear)

* [Draft][draft]
* [Helm][helm]
* [Jenkins][jenkins]
* [Octopus][octopus]
* [Artifactory][artifactory] / [XRay][xray]
* [Legacy Support][legacy-support]
* [Configuration Builder][config-builder]
* [windows-jnlp-agent-windows][windows-jnlp-agent-windows]
* [Tutorial: Deploying Windows Apps with Kubernetes][tutorial-windows-kubernetes]
* [Slides: Deploying Windows Apps with Kubernetes][slides-windows-kubernetes]


<!-- Links Referenced -->

[aks-engine]:                               https://github.com/Azure/aks-engine
[artifactory]:                              https://jfrog.com/artifactory/
[config-builder]:                           https://fluentbytes.com/override-classic-asp-net-web-config-configuration-settings-when-using-docker-containers/
[draft]:                                    https://draft.sh/
[helm]:                                     https://helm.sh/
[gcmessage]:                                https://message.gccollab.ca/home
[jenkins]:                                  https://jenkins.io/
[legacy-support]:                           https://anthonychu.ca/post/aspnet-web-config-transforms-windows-containers-revisited/
[octopus]:                                  https://octopus.com
[pingsvc]:                                  https://gitlab.k8s.cloud.statcan.ca/ndm-cloud-dotnetteam/pingservice
[pongsvc]:                                  https://gitlab.k8s.cloud.statcan.ca/ndm-cloud-dotnetteam/pongservice
[tutorial-windows-kubernetes]:              https://jfrog.com/blog/migrate-nexus-artifactory-manage-binaries-better/
[slack]:                                    https://kubernetes.slack.com
[slides-windows-kubernetes]:                https://schd.ws/hosted_files/kccna18/1f/Deploying%20Windows%20Apps.pdf
[windows-jnlp-agent-windows]:               https://github.com/sylus/jenkins-jnlp-agent-windows
[xray]:                                     https://jfrog.com/xray/
