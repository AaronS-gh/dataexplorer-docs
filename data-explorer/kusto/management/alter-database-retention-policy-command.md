---
title: ".alter database retention policy command- Azure Data Explorer"
description: "This article describes the .alter database retention policy command in Azure Data Explorer."
ms.reviewer: yonil
ms.topic: reference
ms.date: 09/29/2022
---
# .alter database retention policy

Change a database's [retention policy](retentionpolicy.md). The retention policy controls the mechanism that automatically removes data from tables or materialized views. It is used to remove data whose relevance is age-based. The retention policy can be configured for a specific table or materialized view, or for an entire database. The policy then applies to all tables in the database that don't override it.

## Syntax

`.alter` `database` *DatabaseName* `policy` `retention` *PolicyObject*

## Arguments

- *DatabaseName* - Specify the name of the database.
- *PolicyObject* - Define a policy object. For more information, see [retention policy](retentionpolicy.md).

### Example

Sets a retention policy with a 10 day soft-delete period, and enable data recoverability:

````kusto
.alter database MyDatabase policy retention
```
{
    "SoftDeletePeriod" : "10.00:00:00",
    "Recoverability" : "Enabled"
}
```
````
