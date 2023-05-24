---
title:  .alter-merge table merge policy command
description: Learn how to use the .alter-merge table merge policy command to change the table's merge policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge table merge policy

Changes the table's [merge policy](mergepolicy.md). The merge policy defines if and how [Extents (Data Shards)](../management/extents-overview.md) in the cluster should get merged.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` *TableName* `policy` `merge` *PolicyObject*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table.|
|*PolicyObject*|string|&check;|A serialized array of one or more JSON policy objects. For more information, see [merge policy](mergepolicy.md).|

### Example

Change a single property of the policy at the table level, retaining all other properties as before:

```kusto
.alter-merge table [table_name] policy merge ```
{
    "MaxRangeInHours": 24
}```
```
