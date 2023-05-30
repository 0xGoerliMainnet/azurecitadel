---
title: "Upload Blobs"
date: 2023-03-14
author: [ "Richard Cheney" ]
description: "Upload files and work with checksums."
weight: 3
series:
 - immutable
menu:
  side:
    parent: infra-immutable
---

## Overview

On this page we'll upload 

1. single blobs 
1. selected blobs in a directory

## Upload blobs

Working with blobs needs Storage Blob Data Owner, Storage Blob Data Contributor, or Storage Blob Data Reader. (Or a custom role.)

1. Single file example

    ```bash
    az storage blob upload \
      --file "./AzureArcFAQ.pdf" \
      --account-name $storage_account --container-name legal-hold --auth-mode login
    ```

    Example output

    ```json
    {
      "client_request_id": "f8e82802-fef3-11ed-bd8b-735ac4546fe0",
      "content_md5": "7sm3Jvr7TV5CaLVi5ppxjw==",
      "date": "2023-05-30T14:11:53+00:00",
      "encryption_key_sha256": null,
      "encryption_scope": null,
      "etag": "\"0x8DB6117D1EE6102\"",
      "lastModified": "2023-05-30T14:11:53+00:00",
      "request_id": "e8ff56aa-001e-001e-4500-930b31000000",
      "request_server_encrypted": true,
      "version": "2021-06-08",
      "version_id": "2023-05-30T14:11:53.7259778Z"
    }
    ```

    We'll return to the md5sums in the next section.

## Batch uploads

1. Batch file example

    ```bash
    az storage blob upload-batch \
    --source ./backdrops \
    --destination legal-hold --destination-path backdrops \
    --account-name $storage_account --auth-mode login
    ```

Filters

* `--if-modified-since`
* `--if-unmodified-since`
    * both use UTC datetime (*YYYY*-*MM*-*DD*T*hh*:*mm*Z)
    * E.g., `date -d "7 days ago" '+%Y-%m-%dT%H:%MZ'`
* `--pattern *.<ext>`

All \*.vhd files go to page, otherwise block. (Can control with `--type`.) 

Add additional info with either the `--metadata` or `--tags` switches.

See `az storage blob upload --help`.

## Updating versions

The storage account is version enabled. Newer versions of files can be uploaded without losing or changing previous versions.

```bash
export AZURE_STORAGE_ACCOUNT=$(az config get --local storage.account --query value -otsv)
export AZURE_STORAGE_AUTH_MODE=login
```

Create a file, myfile

This is a file.

Upload

az storage blob upload -f myfile -c legal-hold

{
  "client_request_id": "d4a209be-ff0b-11ed-bd8b-735ac4546fe0",
  "content_md5": "jYHV/I2hs67adxfqu308lw==",
  "date": "2023-05-30T17:02:39+00:00",
  "encryption_key_sha256": null,
  "encryption_scope": null,
  "etag": "\"0x8DB612FAD44ABFF\"",
  "lastModified": "2023-05-30T17:02:40+00:00",
  "request_id": "ceca63c6-d01e-000d-1818-932f3d000000",
  "request_server_encrypted": true,
  "version": "2021-06-08",
  "version_id": "2023-05-30T17:02:40.1373183Z"
}


Update


This is a file.

Which has now been edited.

Upload

az storage blob upload -f myfile -c legal-hold --overwrite

{
  "client_request_id": "fd720448-ff0b-11ed-bd8b-735ac4546fe0",
  "content_md5": "LMbezuVcVoyEolNrVB/peg==",
  "date": "2023-05-30T17:03:47+00:00",
  "encryption_key_sha256": null,
  "encryption_scope": null,
  "etag": "\"0x8DB612FD6042EF8\"",
  "lastModified": "2023-05-30T17:03:48+00:00",
  "request_id": "c89fdaf5-101e-0060-3118-939b76000000",
  "request_server_encrypted": true,
  "version": "2021-06-08",
  "version_id": "2023-05-30T17:03:48.5022728Z"
}

Note that the version id, timestamp, etag and conten_md5 are all updated.

az storage blob list -c legal-hold 

custom="{name:name, etag:properties.etag, md5sum:properties.contentSettings.contentMd5, version:versionId, mode:immutabilityPolicy.policyMode, expiry:immutabilityPolicy.expiryTime}"

az storage blob list -c legal-hold --include v --query "[].${custom}"


