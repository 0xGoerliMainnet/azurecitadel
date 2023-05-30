---
title: "Checksums"
date: 2023-03-14
author: [ "Richard Cheney" ]
description: "Translate the checksum from raw to base64."
weight: 4
series:
 - immutable
menu:
  side:
    parent: infra-immutable
---

## Overview

All files get uploaded with an automatic md5sum. Note that the contentMd5 value is a base64 encoded representation of the binary MD5 hash value.

For example:

```bash
md5sum AzureArcFAQ.pdf
```

```text
eec9b726fafb4d5e4268b562e69a718f  AzureArcFAQ.pdf
```

The contentMd5 checksums in the storage account are base64 encoded, e.g.:

```bash
az storage blob show \
  --name AzureArcFAQ.pdf --container-name time-based \
  --account-name $storage_account --auth-mode login \
  --query properties.contentSettings.contentMd5 -otsv
```

```text
7sm3Jvr7TV5CaLVi5ppxjw==
```

## Converting with Bash

* From md5sum to base64 encoded

    ```bash
    md5sum --binary AzureArcFAQ.pdf | awk '{print $1}' | xxd -p -r | base64
    ```

* From base64 encoded to md5sum

    ```bash
    echo "7sm3Jvr7TV5CaLVi5ppxjw==" | base64 -d | xxd -p
    ```

## Converting with PowerShell

* From md5sum to base64 encoded

    ```powershell
    $FilePath = ".\AzureArcFAQ.pdf"
    $rawMD5 = (Get-FileHash -Path $FilePath -Algorithm MD5).Hash
    $hashBytes = [system.convert]::FromHexString($rawMD5)
    $contentMd5 = [system.convert]::ToBase64String($hashBytes)
    ```

* From base64 encoded to md5sum

    ```powershell
    $contentMd5 = "7sm3Jvr7TV5CaLVi5ppxjw=="
    $hashBytes = [system.convert]::FromBase64String($contentMd5)
    $rawMD5 = [system.convert]::ToHexString($hashBytes)
    ```

## Useful commands

- List blobs with checksum

    ```bash
    az storage blob list \
        --container-name legal-hold --account-name $storage_account \
        --query "[].{name:name, md5:properties.contentSettings.contentMd5}" \
        --auth-mode login --output table
    ```
    
1. Filter container based on md5sum base64 value and count array length

    ```bash
    az storage blob list --container-name legal-hold --account-name $storage_account \
    --query "[?properties.contentSettings.contentMd5 == '$contentMd5'] | []]" \
    --auth-mode login
    ```

    Should return one if checksum matches, zero if not. If more than one then multiple matches.

