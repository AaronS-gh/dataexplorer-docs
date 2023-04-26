---
title: .alter-merge database sharding policy command - Azure Data Explorer
description: Learn how to use the .alter-merge database sharding policy command to change the database sharding policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge database sharding policy

Changes the database sharding policy. Use the [sharding policy](../management/shardingpolicy.md) to manage data sharding for databases and tables.  

The sharding policy defines if and how [Extents (data shards)](../management/extents-overview.md) in the Azure Data Explorer cluster should be sealed. When a database is created, it contains the default data sharding policy. All tables created in the database inherit this policy unless the policy is explicitly overridden at the table level.

## Permissions

You must have at least [Database Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `database` *DatabaseName* `policy` `sharding` *ArrayOfPolicyObjects*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*DatabaseName*|string|&check;|The name of the database for which to alter the sharding policy.|
|*ArrayOfPolicyObjects*|string|&check;|A serialized array of one or more JSON policy objects. For more information, see [sharding policy](shardingpolicy.md).|

## Returns

Returns a JSON representation of the policy.

## Example

The following command changes a single property for the sharding policy for a database:

```kusto
.alter-merge database MyDatabase policy sharding 
@'{ "MaxExtentSizeInMb": 1024}'
```
