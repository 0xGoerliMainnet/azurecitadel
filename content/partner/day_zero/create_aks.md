---
title: "Create an AKS cluster"
date: 2023-02-14
author: [ "Richard Cheney" ]
description: "Step through the creation of an Azure Kubernetes Cluster."
draft: false
weight: 4
menu:
  side:
    parent: partner-zero
    identifier: partner-zero-create_aks
series:
 - partner-zero
---

## Introduction

We'll create an AKS cluster in the portal to highlight some o the key architectural decision points and configuration options. This will highight some of the key differences between AKS and EKS or GKS, and also the common ground.

1. Search on AKS and create a cluster

    ![AKS creation screen in the Azure Portal](/partner/day_zero/images/create_a_k8s_cluster.png)

This will start up the wizard.

## Basic

1. Select the **aks** resource group

    You created this using the CLI on the last page.

1. Select one of the presets

    There are a number of Cluster Preset Configurations:

    1. Dev/Test
    1. Cost Optimised
    1. Standard
    1. Batch Processing
    1. Hardened Access

    Click on *Learn more and compare presets* to compare the presets.

    ![Comparing the AKS presets in the Azure Portal](/partner/day_zero/images/aks_presets.png)

    Select **Dev/Test**.

1. Set cluster name
1. Open the Availability Zones dropdown

    The main Azure regions have availability zones. The virtual machine scale sets (VMSS) that form the nodepools in AKS can be placed into a single zone, or distributed across multiple availability zones.

    Check **Zone 1, 2, 3**.

1. Select **Free** pricing tier

    The managed control plane is provided as a free service when this is selected.

    Select the Standard pricing tier for a higher availability SLA.

1. Keep the defaults for Kubernetes Version and Automatic Upgrade
1. Primary Node Pool

    Size, scale and minmax are selectable. The size will be defaulted by he preset, but you can change to preferred VM family and size.

1. Click Next to move Node Pools

## Node pools

We will not be changing anything on this tab, but we'll talk through it quickly.

1. Node pools

    The primary node pool is Linux. Most clusters only have the primary node pool. You can create additional nodepools for workload isolation, node pools with additional VM capability (e.g. GPUs) or to create Windows node pools when deploying Windows Core based pods.

1. Enable virtual nodes

    AKS can use Virtual Kubelet for fast bursting to the ACI service.

1. Node pool OS disk encryption

   All disks are automatically encrpyted using Azure Storage's Microsoft-managed keys.

   You have the option to bring your own keys.

1. Click Next for Access

## Access

1. Resource identity

    The cluster will have a system assigned managed identity. Managed Identities are similar to the AWS IAM Service Role.

    You don't need to manage credentials, as tokens are pulled from the Instance Metedata Service (IMDS) on the trusted resource. The identity managed the load balancer for Kubernetes service, managed disks, etc.

    You can switch to a Service Principal, but this isn't recommended as the credentials need to be managed and will expire.

    Leave unchanged.

1. Authentication and Authorization

    Choose from the three options:

    1. Local accounts with Kubernetes RBAC
    1. Azure AD authentication with Kubernetes RBAC
    1. Azure AD authentication with Azure RBAC

    Azure AD auth gives SSO with MFA, conditional access etc.


## Next

On the next page you will use the Azure CLI and the Cloud Shell to pull down an image into the container registry and you'll also create a virtual network.

## Resources

* <https://learn.microsoft.com/en-us/azure/aks/>
* <https://learn.microsoft.com/en-us/azure/aks/concepts-clusters-workloads>
* <https://learn.microsoft.com/en-us/azure/aks/free-standard-pricing-tiers>
* <https://learn.microsoft.com/en-gb/azure/aks/use-multiple-node-pools>
* <https://learn.microsoft.com/en-gb/azure/aks/virtual-nodes-portal>
* <https://learn.microsoft.com/en-gb/azure/aks/azure-disk-customer-managed-keys>
* <https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview>
* <https://learn.microsoft.com/en-us/azure/aks/use-managed-identity>
* <https://learn.microsoft.com/en-gb/azure/aks/kubernetes-service-principal>
