---
title: Security roles
description: Learn how to use security roles to provide principals access to resources.
ms.reviewer: alexans
ms.topic: reference
ms.date: 07/13/2023
---
# Security roles overview

Principals are granted access to resources through a role-based access control model, where their assigned security roles determine their resource access.

When a principal attempts an operation, the system performs an authorization check to make sure the principal is associated with at least one security role that grants permissions to perform the operation. Failing an authorization check aborts the operation.

The management commands listed in this article can be used to manage principals and their security roles on databases, tables, external tables, materialized views, and functions.

## Management commands

The following table describes the commands used for managing security roles.

|Command|Description|
|--|--|
|`.show`|Lists principals with the given role.|
|`.add`|Adds one or more principals to the role.|
|`.drop`|Removes one or more principals from the role.|
|`.set`|Sets the role to the specific list of principals, removing all previous ones.|

## Security roles

The following table describes the level of access granted for each role and shows a check if the role can be assigned within the given object type.

|Role|Permissions|Databases|Tables|External tables|Materialized views|Functions|
|--|--|--|--|--|--|--|
|`admins` | View, modify, and remove the object and subobjects.|&check;|&check;|&check;|&check;|&check;|
|`users` | View the object and create new subobjects.|&check;|||||
|`viewers` | View the object where [RestrictedViewAccess](restrictedviewaccesspolicy.md) isn't turned on.|&check;|||||
|`unrestrictedviewers`| View the object even where [RestrictedViewAccess](restrictedviewaccesspolicy.md) is turned on. The principal must also have `admins`, `viewers` or `users` permissions. |&check;|||||
|`ingestors` | Ingest data to the object without access to query. |&check;|&check;||||
|`monitors` | View metadata such as schemas, operations, and permissions.|&check;|||||

For a full description of the security roles at each scope, see [Kusto role-based access control](access-control/role-based-access-control.md).

> [!NOTE]
> It isn't possible to assign the `viewer` role for only some tables in the database. For different approaches on how to grant a principal view access to a subset of tables, see [manage table view access](manage-table-view-access.md).

> [!TIP]
> There are three cluster level security roles (AllDatabasesAdmin, AllDatabasesViewer, and AllDatabasesMonitor) that can only be configured in the Azure portal. To learn more, see [manage cluster permissions](../../manage-cluster-permissions.md).

## Common scenarios

### Show the roles on the cluster

To see your own roles on the cluster, run the following command:

`.show` `cluster` `principal` `roles`

To see all roles on the cluster, you must have at least `AllDatabasesMonitor` permissions on the cluster. To see the roles, run the following command:

`.show` `cluster` `principals`

### Show the roles you have on a resource

To check the roles assigned to you on a specific resource, run the following command within the relevant database or the database that contains the resource:

`.show` ( `database` | `table` | `external table` | `function` | `materialized view` ) *ResourceName* `principal` `roles`

### Show the roles of all principals on a resource

To see the roles assigned to all principals for a particular resource, run the following command within the relevant database or the database that contains the resource:

`.show` ( `database` | `table` | `external table` | `function` | `materialized view` ) *ResourceName* `principals`

## Next steps

* Read about [Kusto role-based access control](access-control/role-based-access-control.md)
* Learn how to [reference security principals](access-control/referencing-security-principals.md)
