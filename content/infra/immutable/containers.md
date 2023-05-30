---
title: "Containers"
date: 2023-03-14
author: [ "Richard Cheney" ]
description: "Create and protect a storage account with version level immutability."
weight: 2
series:
 - immutable
menu:
  side:
    parent: infra-immutable
---

## Overview

On this page we'll add two different containers.

1. time-based
1. legal-hold

Both will inherit the account level time based policy of two days.

## Working directory

1. Move to your ~/immutable folder 

    ```bash
    cd ~/immutable
    ```

    This has the `.azure/config` file from the last lab.

1. Retrieve the storage account name

    ```bash
    storage_account=$(az config get --local storage.account --query value -otsv)
    ```

## Time based container 

1. Create a container

    Note that this uses the container-rm subcommand.

    ```bash
    az storage container-rm create \
    --name time-based \
    --storage-account $storage_account  \
    --public-access off --enable-vlw
    ```

1. Check (optional)

    ```bash
    az storage container-rm show \
        --storage-account $storage_account \
        --name time-based \
        --query '[immutableStorageWithVersioning.enabled]' \
        --output tsv
    ```

1. Set a different default period (optional)

    The time based policy from the account level (two days) will be inherited, but you can override. Here we extend that to five days, and permit append blobs to be extended.

    ```bash
    az storage container immutability-policy create \
        --account-name $storage_account \
        --container-name time-based \
        --period 5 \
        --allow-protected-append-writes true
    ```
    
## Legal hold container 

1. Create a container

    ```bash
    az storage container-rm create \
    --name legal-hold \
    --storage-account $storage_account  \
    --public-access off --enable-vlw
    ```

1. Add a legal hold to the container

    Legal hold can be applied at a container level, or on individual blob versions. As this is a target for storing legal hold info then suggest container level.

    ```bash
    az storage container legal-hold set \
      --account-name $storage_account \
      --container-name legal-hold \
      --tags tag1 tag2 \
      --allow-protected-append-writes-all true
    ```

    Note that the tags are required. Use to describe the contents.

## RBAC roles

### Storage account

Example with Blob Reader at the storage account scope.

1. Get the storage account name

    ```bash
    storage_account=$(az config get --local storage.account --query value -otsv)
    ```

1. Add yourself as a Blob Reader

    ```bash
    az role assignment create \
        --role "Storage Blob Data Reader" \
        --scope $(az storage account show --name $storage_account --query id -otsv) \
        --assignee $(az ad signed-in-user show --query id -otsv)
    ```

### Container

Example with Blob Contributor at the container scope.

1. Get the storage account name

    ```bash
    storage_account=$(az config get --local storage.account --query value -otsv)
    ```

1. Get the resource ID for the storage account

    ```bash
    storage_account_id=$(az storage account show --name $storage_account --query id -otsv)
    ```

1. Extend the scope with the container subresource

    ```bash
    container_scope="${storage_account_id}/blobServices/default/containers/legal-hold"
    ```

1. Add yourself as a Blob Contributor

    ```bash
    az role assignment create \
        --role "Storage Blob Data Contributor" \
        --scope $container_scope \
        --assignee $(az ad signed-in-user show --query id -otsv)
    ```

Generally, I would recommend only setting RBAC role assignments at the storage account scope for simplicity.

Note that the assignee could be other users, secuirty groups, service principals or managed identities. 

