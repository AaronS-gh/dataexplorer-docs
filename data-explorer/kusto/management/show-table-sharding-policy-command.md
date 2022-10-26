---
title: .show table sharding policy command - Azure Data Explorer
description: This article describes the .show table sharding policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 10/10/2021
---
# .show table sharding policy

Show the table sharding policy. Use the [sharding policy](../management/shardingpolicy.md) to manage data sharding for databases and tables.  

The sharding policy defines if and how [Extents (data shards)](../management/extents-overview.md) in the Azure Data Explorer cluster should be sealed. When a database is created, it contains the default data sharding policy. This policy is inherited by all tables created in the database (unless the policy is explicitly overridden at the table level).

## Syntax

**For a specified table**

`.show` `table` *TableName* `policy` `sharding`

**For all tables**

`.show` `table` * `policy` `sharding`

## Arguments

*TableName* - Specify the name of the table. A wildcard (*) denotes all tables.

## Returns

Returns a JSON representation of the policy.

## Example

The following example shows the sharding policies for all tables:

```kusto
.show table * policy sharding 
```