---
title: "Storage Account"
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

On this page we'll a storage account with:

1. version level immutability support
1. account level time-based immutability lock
1. change feed

Read the [immutable storage overview](https://learn.microsoft.com/azure/storage/blobs/immutable-storage-overview) first, including [time-based](https://learn.microsoft.com//azure/storage/blobs/immutable-time-based-retention-policy-overview) and [legal holds](https://learn.microsoft.com/azure/storage/blobs/immutable-legal-hold-overview).

## Architectural decisions

To configure an immutability policy that is scoped to a blob version, you must enable support for version-level immutability on either the storage account or a container. This is required for legal hold.

* *Configure storage account with version level immutability with `--enable-alw`* ✅

Once version-level immutability is enabled you can configure a default policy at the account or container level, but only for time-based immutability. The permitted range is 1-146,00 days (400 years).

* *Configure account level time-based retention policy set to 2 days* ✅

[Immutability state](https://learn.microsoft.com/azure/storage/blobs/immutable-time-based-retention-policy-overview#locked-versus-unlocked-policies) can be Disabled, Unlocked or Locked.

* *Configure immutability state of unlocked for testing purposes* ✅

Legal hold must be applied to individual blob versions. This can be configured on existing blob versions or as blobs are uploaded. Container level policies can be enabled at the container level so that new blobs (or blob versions) will automatically be placed on legal hold.

* *Configure container level policy set to legal hold for 1 day* ✅

The blob uploads will take that default container level policy unless the upload stipulates something else.

Additional settings:

✅ *Tighten network access* ✅
✅ *Disable account key access* ✅
✅ *Enable blob versioning* ✅
✅ *Set [change feed](https://learn.microsoft.com/azure/storage/blobs/storage-blob-change-feed) to 7 days* ✅
❌ *Point-in-time restore* (conflicts with Azure Blob backup)

## Working directory

1. Create a directory

    ```bash
    mkdir ~/immutable
    ```

1. Move to it

    ```bash
    cd ~/immutable
    ```

## Variables

1. Input variables

    Customise if required.

    ```bash
    resource_group="immutable"
    location="uksouth"
    ```

1. Set local defaults

    ```bash
    rgId="/subscriptions/$(az account show --query id -otsv)/resourceGroups/$resource_group"
    hash=$(md5sum <<< $rgId | cut -c1-12)

    az config set --local \
      defaults.group=$resource_group \
      defaults.location=$location \
      storage.account=legalhold$hash
    ```

    The last command creates a `.azure/config` file in the current working directory.

    Note that storage.account is a valid default, but we will pull out the value in the commands as some commands do not use it.

1. Suppress warnings (optional)

    This guide uses some newer features so there are warnings. If desired, these can be suppressed, but not in the local config.

    ```bash
    az config set core.only_show_errors=true
    ```

1. Set auth mode

    You can also set defaults for the auth-mode, so that it purely uses RBAC role assignments. This is a good recommendation to avoid any issues with leaked access keys.

    ```bash
    az config set storage.auth_mode=true
    ```

### Resource group

1. Resource group and defaults

    ```bash
    az group create --name $(az config get defaults.group --local --query value -otsv)
    ```

## Storage account

1. Grab the storage account name

    ```bash
    storage_account=$(az config get storage.account --local --query value -otsv)
    ```

1. Base storage account

    ```bash
    az storage account create \
        --name $storage_account \
        --allow-blob-public-access=false \
        --allow-cross-tenant-replication=true \
        --allow-shared-key-access=false \
        --https-only=true \
        --kind=StorageV2 \
        --min-tls-version=TLS1_2 \
        --public-network-access=Enabled \
        --default-action=Allow \
        --sku=Standard_LRS \
        --enable-alw \
        --immutability-period-in-days 2 \
        --immutability-state unlocked \
        --allow-protected-append-writes true
    ```

1. Configure network rules

    Allow your public IP.

    ```bash
    az storage account network-rule add \
        --account-name $storage_account \
        --action=Allow \
        --ip-address=$(curl -sSL https://myexternalip.com/raw)
    ```

    Default to Deny, except for Azure Services (for the backup).

    ```bash
    az storage account update \
        --name $storage_account \
        --public-network-access=Enabled \
        --default-action=Deny \
        --bypass AzureServices
    ```

1. Update blob services.

    Configure the change feed and versioning.

    ```bash
    az storage account blob-service-properties update \
        --account-name $storage_account \
        --enable-versioning true \
        --enable-delete-retention false \
        --enable-container-delete-retention false \
        --enable-change-feed true \
        --change-feed-days 7 \
        --enable-restore-policy false
    ```

## Container

1. Get the storage account name again

    ```bash
    storage_account=$(az config get --local storage.legalhold --query value -otsv)
    ```

1. Create a container

    Note that this uses the container-rm subcommand.

    ```bash
    az storage container-rm create \
    --name legal-hold \
    --storage-account $storage_account  \
    --public-access off --enable-vlw
    ```

1. Check (optional)

    ```bash
    az storage container-rm show \
        --storage-account $storage_account \
        --name legal-hold \
        --query '[immutableStorageWithVersioning.enabled]' \
        --output tsv
    ```

1. What about one with a different default period?

```bash
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

1. Add a legal hold to the container- DON'T DO THIS!!!

    Legal hold can be applied at a container level, or on individual blob versions. As this is a target for storing legal hold info then suggest container level.

    ```bash
    az storage container legal-hold set \
      --account-name $storage_account \
      --container-name legal-hold \
      --tags tag1 tag2 \
      --allow-protected-append-writes-all true
    ```

    Note that the tags are required.

## Add Blob Contributor

This could be a process using managed identity for the uploads or reads, but for this lab we assign a role for ourselves.
