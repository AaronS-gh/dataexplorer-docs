---
title: Manage database security roles - Azure Data Explorer
description: This article describes how to use management commands to view, add, and remove security roles on the database level in Azure Data Explorer.
ms.topic: reference
ms.date: 01/25/2023
---

# Manage database security roles

Azure Data Explorer uses a role-based access control model in which principals get access to resources according to the security roles they're assigned. In this article, you'll learn how to use management commands to [view existing security roles](#view-existing-security-roles) as well as [add and remove security roles](#add-and-remove-security-roles) on the database level.

> [!NOTE]
> To alter database security roles, you must be either a `Database Admin` or an `AllDatabasesAdmin`. For more information, see [role-based access control](access-control/role-based-access-control.md).

## Database level security roles

The following table shows the possible security roles on the database level and describes the permissions granted for each role.

|Role|Permissions|
|--|--|
|`admins` | View, modify, and remove the database and database sub-entities.|
|`users` | View the database and create new database sub-entities.|
|`viewers` | View tables in the database where [RestrictedViewAccess](restrictedviewaccesspolicy.md) isn't turned on.|
|`unrestrictedviewers`| View the tables in the database even where [RestrictedViewAccess](restrictedviewaccesspolicy.md) is turned on. The principal must also have `admins`, `viewers` or `users` permissions. |
|`ingestors` | Ingest data to the database without access to query. |
|`monitors` | View database metadata such as schemas, operations, and permissions.|

## View existing security roles

Before you begin adding or removing principals, use the `.show` command to see which principals and roles are already set on the database.

### Syntax

`.show` `database` *DatabaseName* `principals`

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *DatabaseName* | string | &check; | The name of the database for which to list principals.|

### Returns

A table with a line for each security role assigned to each principal on the database.

### Example

The following command lists all security principals that have access to the `Samples` database.

```kusto
.show database Samples principals
```

**Example output**

|Role |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN|
|---|---|---|---|---|
|Database Samples Admin |Azure AD User |Abbi Atkins |cd709aed-a26c-e3953dec735e |aaduser=abbiatkins@fabrikam.com|

## Add and remove security roles

This section provides syntax, parameters, and examples for adding and removing principals.

### Syntax

*Action* `database` *ObjectName* *Role* `(` *Principal* [`,` *Principal*...] `)` [`skip-results`] [ *Description* ]

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *Action* | string | &check; | The command `.add`, `.drop`, or `.set`. `.add` adds the specified principals, `.drop` removes the specified principals, and `.set` adds the specified principals and removes all previous ones.|
| *ObjectName* | string | &check; | The name of the object for which to add principals.|
| *Role* | string | &check; | The role to assign to the principal. For databases, this can be `admins`, `users`, `viewers`, `unrestrictedviewers`, `ingestors`, or `monitors`.|
| *Principal* | string | &check; | One or more principals. For how to specify these principals, see [principals and identity providers](./access-control/principals-and-identity-providers.md#examples-for-azure-ad-principals).|
| *Description* | string | | Text to describe the change that will be displayed when using the `.show` command.|
| `skip-results` | string | | If provided, the command won't return the updated list of database principals.|

> [!NOTE]
> The `.set` command with `none` instead of a list of principals will remove all principals of the specified role.

### Returns

A table with a line for each principal and their security roles on the database.

### Examples

In the following examples, you'll see how to [add security roles](#add-security-roles-with-add), [remove security roles](#remove-security-roles-with-drop), and [add and remove security roles in the same command](#add-new-security-roles-and-remove-the-old-with-set).

#### Add security roles with .add

The following example adds a principal to the `users` role on the `Samples` database.

```kusto
.add database Samples users ('aaduser=imikeoein@fabrikam.com')
```

The following example adds an application to the `viewers` role on the `Samples` database.

```kusto
.add database Samples viewers ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com')
```

#### Remove security roles with .drop

The following example removes all principals in the group from the `admins` role on the `Samples` database.

```kusto
.drop database Samples admins ('aadGroup=SomeGroupEmail@fabrikam.com')
```

#### Add new security roles and remove the old with .set

THe following example removes existing `viewers` and adds the provided principals as `viewers` on the `Samples` database.

```kusto
.set database Samples viewers ('aaduser=imikeoein@fabrikam.com', 'aaduser=abbiatkins@fabrikam.com')
```

#### Remove all security roles with .set

The following command removes all existing `viewers` on the `Samples` database.

```kusto
.set database Samples viewers none
```
