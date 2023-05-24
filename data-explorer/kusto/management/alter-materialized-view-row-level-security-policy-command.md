---
title:  .alter materialized view row level security policy command
description: Learn how to use the .alter materialized view row level security policy command to enable or disable the materialized view's row level security policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter materialized view row level security policy

Enables or disables a materialized view's [row_level_security policy](rowlevelsecuritypolicy.md). The Row Level Security simplifies the design and coding of security. It lets you apply restrictions on data row access in your application. For example, limit user access to rows relevant to their department, or restrict customer access to only the data relevant to their company.

> [!NOTE]
> Even when the policy is disabled, you can force it to apply to a single query. Enter the following line before the query:
>
> `set query_force_row_level_security;`
>
> This is useful if you want to try various queries for row_level_security, but don’t want it to actually take effect on users. Once you’re confident in the query, enable the policy.

For more information about running queries on the row level security policy, see [row_level_security policy](rowlevelsecuritypolicy.md).

## Permissions

You must have at least [Database Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter` `materialized-view` *MaterializedViewName* `policy` `row_level_security` [`enable` | `disable`]

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*MaterializedViewName*|string|&check;|The name of the materialized view for which to turn on or off the row level security policy.|

### Example

Enable the policy at the materialized-view level:

```kusto
.alter materialized-view MyMaterializeView policy row_level_security enable "AnonymizeSensitiveData"
```

Disable the policy at the materialized-view level:

```kusto
.alter materialized-view MyMaterializeView policy row_level_security disable "AnonymizeSensitiveData"
```
