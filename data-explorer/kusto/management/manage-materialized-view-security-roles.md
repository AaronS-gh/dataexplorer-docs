---
title: Manage materialized view roles - Azure Data Explorer
description: This article describes how to use management commands to view, add, and remove materialized view admins on the materialized view level in Azure Data Explorer.
ms.topic: reference
ms.date: 02/21/2023
---

# Manage materialized view roles

Principals are granted access to resources through a role-based access control model, where their assigned security roles determine their resource access.

On materialized views, the only security role is `admins`. Materialized view `admins` have the ability to view, modify, and remove the materialized view.

In this article, you'll learn how to use management commands to [view existing admins](#view-existing-admins) as well as [add and remove admins](#add-and-remove-admins) on materialized views.

> [!NOTE]
> A principal must have access on the database or table level to be a Materialized View Admin.

## Permissions

You must have Database Admin permissions or be a Materialized View Admin on the specific materialized view to run these commands. For more information, see [role-based access control](access-control/role-based-access-control.md).

## View existing admins

Before you add or remove principals, you can use the `.show` command to see a table with all of the principals that already have admin access on the materialized view.

### Syntax

`.show` `materialized view` *MaterializedViewName* `principals`

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *MaterializedViewName* | string | &check; | The name of the materialized view for which to list principals.|

### Example

The following command lists all security principals that have access to the `SampleView` materialized view.

```kusto
.show materialized view SampleView principals
```

**Example output**

|Role |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN|
|---|---|---|---|---|
|Materialized View SampleView Admin |Azure AD User |Abbi Atkins |cd709aed-a26c-e3953dec735e |aaduser=abbiatkins@fabrikam.com|

## Add and remove admins

This section provides syntax, parameters, and examples for adding and removing principals.

### Syntax

*Action* `materialized view` *MaterializedViewName* `admins` `(` *Principal* [`,` *Principal*...] `)` [`skip-results`] [ *Description* ]

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *Action* | string | &check; | The command `.add`, `.drop`, or `.set`.<br/>`.add` adds the specified principals, `.drop` removes the specified principals, and `.set` adds the specified principals and removes all previous ones.|
| *MaterializedViewName* | string | &check; | The name of the materialized view for which to add principals.|
| *Principal* | string | &check; | One or more principals. For how to specify these principals, see [principals and identity providers](./access-control/referencing-security-principals.md).|
| `skip-results` | string | | If provided, the command won't return the updated list of materialized view principals.|
| *Description* | string | | Text to describe the change that will be displayed when using the `.show` command.|

> [!NOTE]
> The `.set` command with `none` instead of a list of principals will remove all principals.

### Examples

In the following examples, you'll see how to [add admins](#add-admins-with-add), [remove admins](#remove-admins-with-drop), and [add and remove admins in the same command](#add-new-admins-and-remove-the-old-with-set).

#### Add admins with .add

The following example adds a principal to the `admins` role on the `SampleView` materialized view.

```kusto
.add materialized view SampleView admins ('aaduser=imikeoein@fabrikam.com')
```

#### Remove admins with .drop

The following example removes all principals in the group from the `admins` role on the `SampleView` materialized view.

```kusto
.drop materialized view SampleView admins ('aadGroup=SomeGroupEmail@fabrikam.com')
```

#### Add new admins and remove the old with .set

THe following example removes existing `admins` and adds the provided principals as `admins` on the `SampleView` materialized view.

```kusto
.set materialized view SampleView admins ('aaduser=imikeoein@fabrikam.com', 'aaduser=abbiatkins@fabrikam.com')
```

#### Remove all admins with .set

The following command removes all existing `admins` on the `SampleView` materialized view.

```kusto
.set materialized view SampleView admins none
```
