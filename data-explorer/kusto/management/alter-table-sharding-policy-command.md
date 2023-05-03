---
title:  .alter table sharding policy command
description: This article describes the .alter table sharding policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 03/08/2023
---
# .alter table sharding policy

Use this command to change the table sharding policy. Use the [sharding policy](../management/shardingpolicy.md) to manage data sharding for databases and tables.  

The sharding policy defines if and how [Extents (data shards)](../management/extents-overview.md) in your cluster should be sealed. When a database is created, it contains the default data sharding policy. This policy is inherited by all tables created in the database (unless the policy is explicitly overridden at the table level).

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter` `table` *TableName* `policy` `sharding` *PolicyObject*

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *TableName* | string | &check;| The name of the table.|
| *PolicyObject* |string | &check; | A serialized policy object. For more information, see [sharding policy](../management/shardingpolicy.md).|

## Returns

Returns a JSON representation of the policy.

## Example

The following command returns the updated extents sharding policy for the table.

````kusto
.alter table MyTable policy sharding
```
{
    "MaxRowCount" : 750000,
    "MaxExtentSizeInMb" : 1024,
    "MaxOriginalSizeInMb" : 2048
}
```
````
