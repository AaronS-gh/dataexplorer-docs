---
title: .show table merge policy command- Azure Data Explorer
description: Learn how to use the `.show table merge policy` command to display the table's merge policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 05/23/2023
---
# .show table merge policy

Display the table's [merge policy](mergepolicy.md). The merge policy defines if and how [Extents (Data Shards)](../management/extents-overview.md) in the cluster should get merged.

## Permissions

You must have at least Database User, Database Viewer, or Database Monitor to run this command. For more information, see [role-based access control](access-control/role-based-access-control.md).

## Syntax

`.show` `table` *TableName* `policy` `merge`

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table for which to show the policy details.|

## Returns

Returns a JSON representation of the policy.

### Example

Display the policy at the table level:

```kusto
.show table database_name policy merge 
```
