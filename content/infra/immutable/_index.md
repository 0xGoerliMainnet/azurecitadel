---
title: "Immutable Storage"
description: "Short labs showcasing the power of immutable storage accounts for legal hold purposes."
draft: false
weight: 1
menu:
  side:
    identifier: 'infra-immutable'
    parent: 'infra'
---

## Introduction

Azure supports two types of storage account immutability:

1. Time-based
1. Legal hold

In these labs you will create a storage account with blob versions. The storage account will be configured with an account level time based retention policy to covers all upload. Individual blob versions will then be configured with a legal hold.

You will also explore change feeds, manifests, checksums, reporting, Azure Blob immutable backup instances, RBAC controls, cross-tenant private links and more.
