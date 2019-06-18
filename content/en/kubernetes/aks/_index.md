---
date: "2019-04-15T22:00:00-04:00"
title: "Azure Kubernetes Service"
featureconfig:
  title: "Azure Kubernetes Service"
  description: "Discussion around the Azure managed service for Kubernetes."
  status: "Kubernetes"
  weight: 8
lastmod: "2019-04-25"
layout: single
linktitle: managed
authors: ["sylus"]
categories:
  - kubernetes
tags:
  - kubernetes
slug: kubernetes
toc: true
---

{{< youtube IOhfZP6p2kg >}}

## AKS

Azure Kubernetes Service (**AKS**) managed your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized applications without advanced container orchestration expertise. It also eliminates the burden of ongoing operations and maintenance by provisioning, upgrading, and scaling resources on demand, without taking your applications offline.

**Benefits**:

* Master Node(s) are free and handled by Azure
* Refined process for updating, and scaling
* Integrated metrics with Portal
* Allows for Infrastructure as Code model through ARM templates
* Dev Spaces
* Windows GA Support scheduled for July

AKS is now general availability (**GA**) and with that announcement a slew of longstanding issues have been addressed! Most importantly you can now leverage a custom vnet and RBAC with your cluster. These were among the final key blockers for many organizations fully going down the AKS route as opposed to AKS Engine where you still have the master node under your control.

Notably the **AKS** workflow in the Azure portal has been noticably improved and for quick one-off installations I would recommend going that route. Of course for production type deployments always following the infrastructure as code methodology is indeed the way to go! The following templates setup a vnet in a separate "production" resource group where the subnet is given to the actual **AKS** deployment.

* [AKS ARM templates][aks-arm]

### Create VNET resources

Specify the name of your resource group that the custom virtual network resides in.

```sh
export AKS_VNET_RG=network-production-rg
export AKS_VNET_LOCATION=eastus
```

If the resouce group was not created beforehand you can simply create it now.

```sh
az group create -n ${AKS_VNET_RG} \
                -l ${AKS_VNET_LOCATION}
```

We can now instantiate the custom vnet template that will create both a local and containers subnet.

```sh
az group deployment create -n aks-managed-vnet \
                           -g ${AKS_VNET_RG} \
                           --template-file kube-vnet.json \
                           --parameters kube-vnet.parameters.json
```

### Service Principal

Grant the AKS cluster Service Principal access to our custom virtual network using the resource group.

```sh
az ad sp create-for-rbac --name aks-sylus
```

```sh
az role assignment create --role=Contributor \
                          --scope=/subscriptions/SUBSCRIPTION_ID/resourceGroups/${AKS_VNET_RG} \
                          --assignee SP_CLIENTID
```

### Create AKS cluster

In order to create a managed AKS cluster leveraging the custom vnet simply fill out the missing parameters in the `kube-managed.parameters.json` file. These parameters include but are not limited to the following:

* dnsPrefix
* resourceName
* servicePrincipalClientId
* servicePrincipalClientSecret
* sshRSAPublicKey
* vnetSubnetID

```sh
export AKS_RG=aks-sylus-rg
export AKS_NAME=aks-managed-sylus
```

```sh
az group create -n ${AKS_RG} \
                -l ${AKS_VNET_LOCATION}
```

```sh
az group deployment create -n ${AKS_NAME} \
                           -g ${AKS_RG} \
                          --template-file kube-managed.json \
                          --parameters kube-managed.parameters.json
```

### Common Operations

Establish the kubeconfig context and retrieve cluster configuration.

```sh
export AKS_RG=aks-sylus-rg
export AKS_NAME=aks-managed-sylus
```

```sh
az aks get-credentials --resource-group ${AKS_RG} \
                       --name ${AKS_NAME}
```

Similarly to `kubectl proxy` with launch and open the Kubernetes dashboard.

```sh
az aks browse --resource-group ${AKS_RG} \
              --name ${AKS_NAME}
```

Will delete the managed cluster service alongside with all its dependencies.

```sh
az group delete --name ${AKS_RG}
```

This will enable dev spaces for the deployed AKS cluster.

```sh
az aks use-dev-spaces -g ${AKS_RG} \
                      -n ${AKS_NAME} \
                      -s develop/sylus -y
```

## AKS-Engine

> For operators that need complete control and customizability of a Kubernetes cluster.

AKS Engine provides convenient tooling to quickly bootstrap Kubernetes cluster on Azure. By leveraging ARM (Azure Resource Manager), AKS Engine helps you create, destroy and maintain clusters provisioned with basic IaaS resources in Azure. AKS Engine is also the open source upstream used by AKS for performing these operations to provide the managed service implementations. One of the biggest differences that you control the master node (and associated costs) and much of the internal operations such as maintenance and upgrading.

This is useful for a few scenarios:

* Running hybrid Linux / Windows nodes (June for AKS)
* Master node ownership which allows for use of features
* Customization of the API-Server
* Advanced networking

### Cluster Definition

aks-engine reads a JSON cluster definition and generates a number of files that may be submitted to Azure Resource Manager (ARM). The generated
files include:

* apimodel.json: Expanded version of the cluster definition provided to the generation command
* azuredeploy.json: Represents a complete description of all Azure resources required to fulfill the cluster definition
* azuredeploy.parameters.json: the parameters file holds a series of custom variables which are used in various locations throughout the deployment

Normally you can simply run acs-engine deploy to do a one-off cluster, however as we are building lots of clusters we are generating our templates first,
and then running the deployment. This has advantages because we can iterate over a bunch of different cluster declarations, and then run them through
acs-engine generate to get the definitive template/params for sending to ARM.

The following example illustrates a base cluster definition that we pass to AKS Engine to generate our full template.

{{< highlight json >}}
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "orchestratorRelease": "1.12",
      "kubernetesConfig": {
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
          "--max-pods": "90",
          "--network-plugin": "cni"
        },
        "gcHighThreshold":85,
        "gcLowThreshold": 80,
        "apiServerConfig": {
          "--allow-privileged": "true",
          "--anonymous-auth": "false"
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
          ...
        ]
      }
    },
    "aadProfile": {
      "serverAppID": "XXXXXX",
      "clientAppID": "XXXXXX",
      "tenantID":    "XXXXXX"
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "k8s-aks-environment",
      "vmSize": "Standard_D4s_v3",
      "OSDiskSizeGB": 200,
      "vnetSubnetId": "/subscriptions/XXXXXX/resourceGroups/network-XXXX-rg/providers/Microsoft.Network/virtualNetworks/XXXX-VNet/subnets/Container",
      "firstConsecutiveStaticIP": "XXX.XX.XX.5",
      "vnetCidr": "XXX.XX.XX.0/24"
    },
    "agentPoolProfiles": [
      {
        "name": "linuxpool1",
        "count": 1,
        "customNodeLabels": {
          "os": "linux"
        },
        "vmSize": "Standard_D4s_v3",
        "OSDiskSizeGB": 200,
        "storageProfile" : "ManagedDisks",
        "availabilityProfile": "AvailabilitySet",
        "vnetSubnetId": "/subscriptions/XXXXXX/resourceGroups/network-XXXX-rg/providers/Microsoft.Network/virtualNetworks/XXXX-VNet/subnets/Container"
      },
      {
        "name": "windowspool1",
        "count": 1,
        "customNodeLabels": {
          "os": "windows"
        },
        "osType": "Windows",
        "vmSize": "Standard_D4s_v3",
        "OSDiskSizeGB": 200,
        "storageProfile" : "ManagedDisks",
        "availabilityProfile": "AvailabilitySet",
        "vnetSubnetId": "/subscriptions/XXXXXX/resourceGroups/network-XXXX-rg/providers/Microsoft.Network/virtualNetworks/XXXX-VNet/subnets/Container"
      }
    ],
    "linuxProfile": {
      "adminUsername": "XXXXXX",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "ssh-rsa XXXXXX"
          }
        ]
      }
    },
    "windowsProfile": {
      "adminUsername": "XXXXXX",
      "adminPassword": "XXXXXX",
      "windowsPublisher": "MicrosoftWindowsServer",
      "windowsOffer": "WindowsServerSemiAnnual",
      "windowsSku": "Datacenter-Core-1803-with-Containers-smalldisk"
    },
    "servicePrincipalProfile": {
      "objectid": "XXXXXX",
      "clientId": "XXXXXX",
      "secret": "XXXXXX"
    }
  }
}
{{< /highlight >}}

### Installation

With the baseline cluster definition in hand you can now pass the file / template to AKS Engine which will generate the corresponding ARM templates for you. Additionally all of the Keys, Certificates, and KubeConfig files for every Azure supported region are also generated by the binary.

```sh
aks-engine generate --api-model=k8s.environment.official.json
```

### Deployment

Leveraging the generated ARM templates you can now deploy a Kubernetes cluster on the Azure infrastructure in your chosen resource group. Please note that your user and service principal will need to have appropriate IAM permissions in order for the deployment to succeed. If ever there is a problem during the installation you can also add the "–debug" flag which will show you detailed information about each step that is performed.

```sh
az group deployment create --name "k8s-aks-environment" \
                          --resource-group "kubernetes-environment-rg" \
                          --template-file "./_output/k8s-aks-environment/azuredeploy.json" \
                          --parameters "./_output/k8s-aks-environment/azuredeploy.parameters.json"
```

<!-- Links Referenced -->

[aks-arm]:               https://github.com/sylus/aks-iac
[katacoda]:              https://katacoda.com
[katacoda-sylus]:        https://katacoda.com/sylus
