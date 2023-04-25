---
title: .alter-merge table sharding policy command - Azure Data Explorer
description: Learn how to use the .alter-merge table sharding policy command to change the table's sharding policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge table sharding policy

Changes the table sharding policy. Use the [sharding policy](../management/shardingpolicy.md) to manage data sharding for databases and tables.  

The sharding policy defines if and how [Extents (data shards)](../management/extents-overview.md) in your cluster should be sealed. When a database is created, it contains the default data sharding policy. This policy is inherited by all tables created in the database (unless the policy is explicitly overridden at the table level).

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` *TableName* `policy` `sharding` *PolicyObject*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table.|
|*PolicyObject*|string|&check;|A serialized array of one or more JSON policy objects. For more information, see [sharding policy](shardingpolicy.md).|

## Returns

Returns a JSON representation of the policy.

## Example

The following command changes a single property for the sharding policy for a table:

```kusto
.alter-merge table MyTable policy sharding 
@'{ "MaxExtentSizeInMb": 1024}'
```
