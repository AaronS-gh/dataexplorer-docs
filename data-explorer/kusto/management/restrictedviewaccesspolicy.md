---
title: Restricted view access policy - Azure Data Explorer
description: This article describes the restricted view access policy in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 02/19/2020
---
# Restricted view access policy

*RestrictedViewAccess* is an optional policy that can be enabled for tables of a database.

When this policy is enabled for a table, data in the table can only be queried by principals who have an [UnrestrictedViewer](../management/access-control/role-based-authorization.md) role in the database.
Any principal,  who isn't registered with an [UnrestrictedViewer](../management/access-control/role-based-authorization.md) database-level role, won't be able to query data in the table. Even an unregistered table/database/cluster admin.

The [UnrestrictedViewer](../management/access-control/role-based-authorization.md) role grants view permission to *all* tables in the database that have the policy enabled.
The current principal, a database admin/user/viewer, is already authorized to query the database. 
Adding or removing principals can be done by a [DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!NOTE]
> RestrictedViewAccess policy can't be configured on a table on which a [Row Level Security policy](./rowlevelsecuritypolicy.md) is enabled.

For more information, see the control commands for managing the [RestrictedViewAccess policy](./show-table-restricted-view-access-policy-command.md).
