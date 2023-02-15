---
title: "Getting access"
date: 2023-02-14
author: [ "Richard Cheney" ]
description: "What is AAD, Azure and M365? WHat are the RBAC role models? How do I grant access to create Azure resources?"
draft: false
weight: 1
menu:
  side:
    parent: partner-zero
    identifier: partner-zero-rbac
series:
 - partner-zero
---

## Portal

OK, let's start with accessing the portal and check if you can see your subscription.

1. Open the Azure Portal

    Open <https://portal.azure.com> in a new window.

1. Authenticate with your ID and password
1. You will now be on the [home page](https://portal.azure.com/#home)

    Do you see this screen?

    ![Screen saying Welcome to Azure and prompting to get a subscription](/partner/day_zero/images/nosubscriptions.png)

    This will because either

    * you don't have a subscription
    * you don't have access to see your subscription

    Don't worry. If it is the latter then we'll elevate your access and assign a role.

## AAD v Azure v M365 v D365

Microsoft has multiple public cloud services for consumers and enterprises.

We will be looking at purely AAD and Azure in these pages, but let's have a brief level set:

* Azure Active Directory (AAD)

    [Azure Active Directory](https://learn.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) is a cloud based identity and access management service.

    AAD also has a broad set of identity functionality - some of which is opened up by Premium P1 and P2 licences - covering multi-factor authentication, conditional access, privileged identity management, access reviews, identity protection, and more.

    It is common for enterprises to [sync](https://learn.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync) their on premises Active Directory environments with AAD.

    Each [AAD tenant](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview) has it's own tenant ID and a directory containing member users, security groups, any invited guest users, plus the ability to create app registrations (for applications to use) as well as system IDs called service principals and managed identities. More on those later.

    There are numerous roles within AAD, but the key one is Global Administrator.

* Azure

   Azure is Microsoft's public cloud service for IaaS and PaaS.

   Azure has its own [RBAC model](https://learn.microsoft.com/azure/role-based-access-control/overview) under the Azure Resource Manager. There are a wide number of [Azure built-in roles](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles) and you can create custom roles as well, but the most commonly used are the general roles:

   * Contributor

      Can create, read, update and delete resources. Cannot create or delete RBAC role assignments.

  * Reader

       Can view all resources. No permissions to create, read or update.

  * User Access Administator

      Can also read resources, plus access to create and delete role assignments.

  * Owner

      Full access for resources and authorisations.

* Microsoft 365 (M365) / Office 365 (O365)

    SaaS service for Outlook, Teams, SharePoint, OneDrive, Word, Excel, PowerPoint, OneNote and much more.

* Dynamics 365, Power Platform

    SaaS CRM system, plus low code / no code capabilities.

The above is a massive simplification, but will do for now.

## AAD Users and Groups
## Elevate

1. Click on [Subscriptions](https://portal.azure.com/#view/Microsoft_Azure_Billing/SubscriptionsBlade) in the Navigate section

    You will see all of the subscriptions that you are permitted to view.

## Resources

* <https://learn.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis>
* <https://learn.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync>
* <https://learn.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles>
* <https://learn.microsoft.com/azure/active-directory/roles/permissions-reference>
* <https://learn.microsoft.com/azure/role-based-access-control/overview>
* <https://learn.microsoft.com/azure/role-based-access-control/built-in-roles>