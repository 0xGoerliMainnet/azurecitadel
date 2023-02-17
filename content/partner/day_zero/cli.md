---
title: "Azure CLI and Cloud Shell"
date: 2023-02-14
author: [ "Richard Cheney" ]
description: "Create a simple resource and resource group via the portal. Learn about providers and resourceIds."
draft: false
weight: 3
menu:
  side:
    parent: partner-zero
    identifier: partner-zero-cli
series:
 - partner-zero
---

## Install the Azure CLI

The Azure CLI, az, is usually preferred over Az PowerShell cmdlets by those moving from using awscli. It works well in Bash scripts, and also utilises JMESPATH queries for drilling into JSON output.

On this page we will use the Cloud Shell, but it is recommended to [install the Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli) locally.

> For those of you on Windows 10 and 11, we'd recommend installing [Windows Subsystem for Linux](https://learn.microsoft.com/windows/wsl/install). By default that will install Ubuntu 22.04, and then you can install [az](https://learn.microsoft.com/cli/azure/install-azure-cli-linux?pivots=apt), [terraform](https://developer.hashicorp.com/terraform/cli/install/apt) etc. using standard apt instructions for Debian systems. (WSL supports many other distros.)
>
> The [Windows Terminal](https://aka.ms/terminal) is also highly recommended as it natively creates profiles for your WSL distros, and it also works with Cloud Shell.

## Cloud Shell

The Azure [Cloud Shell](https://learn.microsoft.com/azure/cloud-shell/overview) gives browser based access to a container based on an [image](https://github.com/Azure/CloudShell) with common tools such as the Azure CLI, Ansible, Terraform, kubectl, jq and more. Cloud Shell supports both Bash and PowerShell experiences,although both are based on Linux Mariner.

1. Open Cloud Shell, e.g.:

    * open <https://shell.azure.com>, or
    * click on the Cloud Shell prompt at the top of the Azure portal

1. Select *Bash*
1. Create storage

    This is a one-off activity, and will create a storage account to

    * persist your home directory in a page blob, and
    * provide the fileshare mounted as `~/clouddrive`

1. Type `code .`

    Cloud Shell includes the Monaco editor with language highlighting and explorer.

1. Exit the editor with `CTRL`+`Q`

## Azure CLI

Anyone familiar with the AWS CLI should find themselves at home with the Azure CLI. A few useful links:

* Azure CLI documentation  - <https://aka.ms/cli>
* [Getting started](https://learn.microsoft.com/cli/azure/get-started-with-azure-cli)

    Includes `az login`, `az account show`, `az account set --subscription <guid>`, plus globals such as `--query` and `--output`

* [Configuration](https://learn.microsoft.com/cli/azure/azure-cli-configuration)

    Config the default output style with `az init`, defaults with `az config` and env vars

* [Persisted parameters](https://learn.microsoft.com/cli/azure/param-persist-howto)

    Variable precedence:

    1. Command-line parameters
    1. Values in the local working directory set by `az config param-persist`
    1. Environment variables
    1. Values in the configuration file or set with az config

## Push image

As an example, here are a few commands to push an image into the Azure Container Registry. This will use the [quickstart](https://learn.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) as a reference point, but we've already got the container registry and we'll use a different image.

> Note that `CTRL`+`SHIFT`+`V` will paste from the clipboard into Cloud Shell.


1. Show the current subscription context

    ```bash
    az account show --output jsonc
    ```

1. List out the resource groups

    ```bash
    az group list --output table
    ```

    You should see the `acr` resource group, plus the one that Cloud Shell has created.

1. List the container registries in yamlc format

    ```bash
    az acr list --resource-group acr --output yamlc
    ```

1. Grab the resource ID

    ```bash
    acr=$(az acr list --resource-group acr --query [0].name --output tsv)
    ```

    Assumes only one ACR exists in that resource group.

1. Log in to the registry

    ```bash
    az acr login --name $acr
    ```






## Next

On the next page you will use the Azure CLI and the Cloud Shell to pull down an image into the container registry and you'll also create a virtual network.

## Resources

* <https://learn.microsoft.com/azure/azure-resource-manager/management/>
