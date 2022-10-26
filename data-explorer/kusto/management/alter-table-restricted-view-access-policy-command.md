---
title: .alter restricted view access policy command - Azure Data Explorer
description: This article describes the .alter restricted view access policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 09/27/2021
---
# .alter restricted view access policy

Enable or disable the optional table [restricted view access policy](restrictedviewaccesspolicy.md). When this policy is enabled for a table, data in the table can only be queried by principals who have an [UnrestrictedViewer](../management/access-control/role-based-authorization.md) role in the database. 

## Syntax

Enable or disable the policy for one table:

`.alter` `table` *TableName* `policy` `restricted_view_access` `true`|`false`

Enable or disable the policy for several tables:

`.alter` `tables` (*TableName*,*TableName2*,*TableName3*,...) `policy` `restricted_view_access` `true`|`false`

## Arguments

*TableName* - Specify the name of the table. 

## Examples

### Enable a policy for a table

```kusto
.alter table MyTable policy restricted_view_access true
```

### Disable the policy of several tables

```kusto
.alter tables (Table1, Table2, Table4) policy restricted_view_access false
```
