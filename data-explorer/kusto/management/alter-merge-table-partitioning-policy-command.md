---
title:  .alter-merge table policy partitioning command
description: Learn how to use the `.alter-merge table policy partitioning` command to change the table's partitioning policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge table policy partitioning command

Changes the table's [partitioning policy](partitioningpolicy.md). The partitioning policy defines if and how [extents (data shards)](../management/extents-overview.md) should be partitioned for a specific table or a [materialized view](materialized-views/materialized-view-overview.md).

## Permissions

You must have at least [Database Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` *TableName* `policy` `partitioning` *PolicyObject*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table.|
|*PolicyObject*|string|&check;|A serialized array of one or more JSON policy objects. For more information, see [partitioning policy](partitioningpolicy.md).|

### Example

Alter merge the policy at the table level:

```kusto
.alter-merge table MyTable policy partitioning '{"EffectiveDateTime":"2023-01-01"}'
```
