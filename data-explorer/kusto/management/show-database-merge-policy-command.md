---
title: .show database merge policy command- Azure Data Explorer
description: This article describes the .show database merge policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 09/29/2021
---
# .show database merge policy

Display a database's [merge policy](mergepolicy.md). The merge policy defines if and how [Extents (Data Shards)](../management/extents-overview.md) in the cluster should get merged. 

## Syntax

`.show` `database` *DatabaseName* `policy` `merge` 

## Arguments

*DatabaseName* - Specify the name of the database.

## Returns

Returns a JSON representation of the policy.

### Example

Display the policy at the database level:

```kusto
.show database database_name policy merge 
```
