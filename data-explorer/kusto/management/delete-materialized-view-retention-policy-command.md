---
title: .delete materialized-view retention policy command- Azure Data Explorer
description: This article describes the .delete materialized-view retention policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 10/03/2021
---
# .delete materialized-view retention policy

Delete a materialized-view's [retention policy](retentionpolicy.md). The retention policy controls the mechanism that automatically removes data from tables or materialized views. It is used to remove data whose relevance is age-based. The retention policy can be configured for a specific table or materialized view, or for an entire database. The policy then applies to all tables in the database that don't override it.

## Syntax

`.delete` `materialized-view` *DatabaseName* `policy` `retention` 

### Example

Delete a retention policy:

```kusto
.delete materialized-view MyMaterializedView policy retention
```
