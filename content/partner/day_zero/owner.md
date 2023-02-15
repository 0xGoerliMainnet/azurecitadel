---
title: "Global Admin and Owner"
date: 2023-02-14
author: [ "Richard Cheney" ]
description: "What is AAD, Azure and M365? What are the RBAC role models? How do I grant access to create Azure resources?"
draft: false
weight: 1
menu:
  side:
    parent: partner-zero
    identifier: partner-zero-owner
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

    This will because either you don't have a subscription, or you don't have access to see your subscription.

    Don't worry - if it is the latter then we'll elevate your access and assign a role. In the meantime, some theory,

## AAD v Azure v M365 v D365

Microsoft has multiple public cloud services for consumers and enterprises. We will be looking purely at Azure AD and Azure public cloud in these pages, but let's have a brief level set:

* Azure Active Directory (AAD)

  [Azure Active Directory](https://learn.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) is a cloud based identity and access management service. It is common for enterprises to [sync](https://learn.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync) their on premises Active Directory environments with AAD.

  Each [AAD tenant](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview) has it's own tenant ID and a directory containing member users, security groups, any invited guest users, plus the ability to create app registrations (for applications to use) as well as system IDs called service principals and managed identities. More on those later. AAD also has a broad set of identity functionality - some of which is opened up by the Premium P1 and P2 licences - covering multi-factor authentication, conditional access, privileged identity management, access reviews, identity protection, and more.

  Azure AD has a few portals (including the [Microsoft Entra](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/TenantOverview.ReactView)) but we will stick with the one within the [Azure Portal](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview).  Check out:

  * [Users](https://portal.azure.com/#view/Microsoft_AAD_UsersAndTenants/UserManagementMenuBlade/~/AllUsers)
  * [Groups](https://portal.azure.com/#view/Microsoft_AAD_IAM/GroupsManagementMenuBlade/~/AllGroups)
  * [Roles and administrators](https://portal.azure.com/#view/Microsoft_AAD_IAM/RolesManagementMenuBlade/~/AllRoles/adminUnitObjectId//resourceScope/%2F) (Global Administrator is the most important role)
  * [Properties](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Properties)

* Azure

    Azure is Microsoft's public cloud service for IaaS and PaaS.

* Microsoft 365 (M365) / Office 365 (O365)

    SaaS service for Outlook, Teams, SharePoint, OneDrive, Word, Excel, PowerPoint, OneNote and much more.

* Dynamics 365

    SaaS CRM system, plus low code / no code capabilities with the Power Platform.

The above is a massive simplification, but will do for now.

## Elevate access in AAD

As mentioned, the Global Administrator role is the most powerful role in AAD.  You can view the role in [Roles and administrators](https://portal.azure.com/#view/Microsoft_AAD_IAM/RolesManagementMenuBlade/~/AllRoles/adminUnitObjectId//resourceScope/%2F) and see who has access.

As a Global Admins you can [elevate their access](https://learn.microsoft.com/azure/role-based-access-control/elevate-access-global-admin) to manage Azure management groups and security groups:

1. Open the [Azure Portal](https://portal.azure.com)
1. [AAD | Properties](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Properties)
1. Toggle *Access management for Azure resources*

    ![Elevate Global Admin access to manage Azure resources](/partner/day_zero/images/elevate.png)

1. Save

> Switching the toggle to on effectively assigns the user as *User Access Administrator* at the root of the Azure RBAC hierarchy.

You can now create Azure RBAC role assignments.

## Azure RBAC

Azure has its own [RBAC model](https://learn.microsoft.com/azure/role-based-access-control/overview) under the Azure Resource Manager.

### Role Assignments

Role assignments are found in **Identity and Access Management** have three elements:

* identity (the AAD objectId for a user, group, service principal or managed identity)
* role definition
* scope

RBAC role assignments are hereditary.

### Role Definitions

There are a wide number of [Azure built-in roles](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles) and you can create custom roles as well. The most commonly used are the general roles:

* **Contributor** | Can create, read, update and delete resources. Cannot create or delete RBAC role assignments.
* **Reader** | Can view all resources. No permissions to create, read or update.
* **User Access Administrator** | Can also read resources, plus access to create and delete role assignments.
* **Owner** | Full access for resources and authorisations.

If you look at the roles you will see they define what the role can do in terms of the actions and notAction arrays. This relates to the REST API methods against the control plane, `https://management.azure.com`. (Sovereign clouds have a different endpoint.)

Some roles, such as Storage Blob Data Contributor, also have dataActions and notDataActions. These refer to the data plane, i.e. the APIs for the PaaS service endpoints.

### Scopes

The most common [RBAC scope](https://learn.microsoft.com/azure/role-based-access-control/scope-overview) levels when you are working with Azure subscriptions are the subscription scope itself and within that, resource groups. Resources then sit within the resource groups and are also a valid scope point.

> It is common to group the resources for an application together, especially if the resources have the same lifecycle, i.e. they are created together or deleted together.

Above the subscription scope you have [Management Groups](https://learn.microsoft.com/en-us/azure/governance/management-groups/overview). In the [portal view](https://portal.azure.com/#view/Microsoft_Azure_ManagementGroups/ManagementGroupBrowseBlade/~/MGBrowse_overview) you will see the default Tenant Root Group.

You can also create additional management groups up to six levels of depth. Above everything is the root.

> Management groups are used extensively by [Azure Landing Zones](https://aka.ms/alz) as a scope point for Azure RBAC role definitions/assignments and Azure Policy definitions/assignments.

| Scope | Example |
|:---|:---|
| root | `/` |
| tenantRootGroup | `/<tenantId>` |
| managementGroup | `/<tenantId>/contoso/sandbox` |
| subscription | `/subscriptions/<subscriptionId>` |
| resourceGroup | `/subscriptions/<subscriptionId>/resourceGroups/myResourceGroupName` |
| resource | `/subscriptions/<subscriptionId>/resourceGroups/myResourceGroupName/providers/Microsoft.ContainerService/managedClusters/myAksClusterName` |

All of these are valid scopes for an RBAC role assignment.

## Assign Owner

OK, enough theory. Assign yourself as Owner:

1. Open the [Azure Portal](https://portal.azure.com)
1. View the [subscriptions](https://portal.azure.com/#view/Microsoft_Azure_Billing/SubscriptionsBlade)

    You should now see the subscription(s). You may need to wait for the RBAC role to propagate, or log out of the portal and back in again.

1. Select the subscription
1. Select *Access control (IAM)*
1. *+ Add | Add role assignment*
1. Select *Owner* role
1. Select yourself as the member
1. *Review + assign*

    ![Assign the Owner RBAC role](/partner/day_zero/images/assign.png)

Feel free to toggle off the Global Admin elevation.

## Next

OK, you can now see the subscription and have full access with both Global Admin (AAD) and Owner (Azure RBAC).

In the next page we'll create a resource group and resource and get to know about providers and resource IDs.

## Resources

* <https://learn.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis>
* <https://learn.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync>
* <https://learn.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles>
* <https://learn.microsoft.com/azure/active-directory/roles/permissions-reference>
* <https://learn.microsoft.com/azure/role-based-access-control/overview>
* <https://learn.microsoft.com/azure/role-based-access-control/built-in-roles>
* <https://learn.microsoft.com/azure/role-based-access-control/elevate-access-global-admin>
* <https://learn.microsoft.com/azure/role-based-access-control/scope-overview>
* <https://learn.microsoft.com/azure/governance/management-groups/overview>
