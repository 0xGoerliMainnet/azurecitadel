---
title: "Providers and Resource IDs"
date: 2023-02-14
author: [ "Richard Cheney" ]
description: "Create a simple resource and resource group via the portal. Learn about providers and resourceIds."
draft: false
weight: 2
menu:
  side:
    parent: partner-zero
    identifier: partner-zero-providers
series:
 - partner-zero
---

## Azure Resource Manager

There are numerous ways for users and systems to drive Azure. Regardless of whether you are using the portal, the CLIs (Azure CLI and PowerShell cmdlets), SDKs, ARM/Bicep templates, Terraform, Pulumi etc., all of the requests eventually go through the REST API for the Azure Resource Manager, or ARM.

> These pages will restrict to the portal, Azure CLI and Terraform.

All requests pass through both the Azure RBAC engine and the Azure Policy engine to see if they are valid and permitted.

## Portal

First, we'll create an Azure Container Registry (ACR) via the portal.

1. Open <https://portal.azure.com>
1. Open [Container registries](https://portal.azure.com/#view/HubsExtension/BrowseResourceGroups)

    * filter All Service in the top left hamburger flyout (star to add to favourites), or
    * searching on "Container registries" in the blue search bar

1. *Create container registry*
    * Create  a new resource group, e.g. *acr*
    * Registry name will form a public FQDN, `<myacrname>.azurecr.io`, and needs to be globally unique
    * Set Location to the *West Europe* region

    > Azure has 60+ regions grouped in [geographies](https://azure.microsoft.com/explore/global-infrastructure/geographies/#overview).


    * Set SKU to *Basic*
    * *Review + create*
    * *Create*

    ![Validating and creating an Azure Contriner Registry in the Azure Portal]](/partner/day_zero/images/create_acr.png)

    > All portal deployments acrually create ARM templates on the fly. (Feel fere to check Settings | Deployments when viewing the resource group).

      Once the deployment is complete then navigate go to the newly created Container Registry resource.

1. Overview

    The Overview pane show key information, including links to the resource group and subscription, plus you have the various settings and configuration available in the blade on the left. Across the top are the breadcrumbs.

    ![Overview pane for an Azure Container Registry](/partner/day_zero/images/acr_overview.png)

1. JSON View

    Click on the JSON View at the top right of the Overview.

    ![JSON View for the Azure Container Registry](/partner/day_zero/images/json_view.png)

    It is useful to create resources manually un the portal to view the properties and values in the resulting JSON object. This can be translated to switch options on the Azure CLI, or for the Terraform resource.

    Note the resource ID. This identifier is a concatenation of:

    |Section|Example|
    |:---|:---|
    |  | *`subscriptions`* |
    | subscription GUID| `c419cbe3-6b84-458a-950a-b4e0d961d1d7` |
    |  | *`resourcegroups`* |
    | resource group name | `acr` |
    |  | *`providers`* |
    | provider type (or namespace) | `Microsoft.ContainerRegistry/registries` |
    | resource name | `richeneyacr` |

## Providers

The provider for container registries is `Microsoft.ContainerRegistry`.

> Each Azure product group maintains its own providers.

Providers need to be registered within a subscription for use. Deploying via the portal does this automatically, but you may need to register providers on newly create subscriptions when automating as a one off task.

Ensure the following are registered:

* Microsoft.Compute
* Microsoft.Network
* Microsoft.Storage
* Microsoft.ContainerService
* Microsoft.Datadog

1. *Portal*
1. Subscriptions | Settings | Resource Providers
1. Select a provider, (e.g. Microsoft.ContainerService)
1. *Register*

   Repeat.

On the next page you'll start using the Azure CLI.

## Documentation

1. Search on "azure docs" in your browser, or <https://aka.ms/azure/docs>.
1. Filter on Containers

    ![Azure Docs page filtered to the Container services](/partner/day_zero/images/azure_docs.png)

1. Open the Container Registry docs area

    ![Azure Container Registry documentation page](/partner/day_zero/images/acr_docs.png)

    The documentation has the expected overview, quickstarts, tutorials and how to guides.

    > The portal pages often have links taking you through to key documentation.

    There is also a useful Reference section that links through to the relevant pages in the Azure CLI docs, the REST API reference and more.

    ![Azure Container Registry REST API reference page](/partner/day_zero/images/acr_rest_api.png)

1. Terraform

    The Terraform documemntation is maintained within the azurerm Terraform repo.

    Use <https://aka.ms/terraform> and then filter on the resource type.

    ![Azure Container Registry Terraform documentation page](/partner/day_zero/images/acr_terraform.png)


## Next

On the next page you will use the Azure CLI and the Cloud Shell to pull down an image into the container registry and you'll also create a virtual network.

## Resources

* <https://learn.microsoft.com/azure/azure-resource-manager/management/>
* <https://learn.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types>
* <https://learn.microsoft.com/azure/azure-portal/>
* <https://aka.ms/azure/docs>
* <https://aka.ms/terraform>
