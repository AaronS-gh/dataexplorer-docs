---
title:  .alter-merge cluster policy capacity command
description: Learn how to use the `.alter-merge cluster policy capacity` command to turn on or turn off a cluster's capacity policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge cluster policy capacity command

Turns on or turns off a cluster's [capacity policy](capacitypolicy.md). The policy is used to control the computational resources for data management operations on the cluster. This command requires [AllDatabasesAdmin](access-control/role-based-access-control.md) permission.

## Permissions

You must have [AllDatabasesAdmin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `cluster` `policy` `capacity` *SerializedArrayOfPolicyObjects*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*SerializedArrayOfPolicyObjects*|string|&check;|A serialized array with one or more JSON policy objects. For more information, see [capacity policy](capacitypolicy.md).|

### Examples

Alter a single property in the cluster level policy, keeping all other properties intact:

```kusto
.alter-merge cluster policy capacity ```
{
  "ExtentsPartitionCapacity": {
    "MaximumConcurrentOperationsPerNode": 4
  }
}```
```
