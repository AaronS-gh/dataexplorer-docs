---
title: Show encoding policy - Azure Data Explorer
description: This article describes the `.show encoding policy` command in Azure Data Explorer.
ms.reviewer: alexans
ms.topic: reference
ms.date: 06/28/2022
---
# .show encoding policy

Shows the encoding policy. For an overview of the encoding policy, see [Encoding policy](encoding-policy.md).
This command requires [table monitor](access-control/role-based-authorization.md) permission.

## Syntax

`.show` *EntityType* *EntityIdentifier* `policy` `encoding`

## Arguments

Name | Description | Values
---|---|---
| *EntityType*| This field determines the type of *EntityIdentifier* |`database` or `table` or `column`
|*EntityIdentifier* | The identifier for the entity. | Column references must include the table name scope. A wildcard `*` is allowed |

## Example

```kusto
.show database Samples policy encoding
```

## See also

* [Encoding policy](encoding-policy.md)
* [.alter encoding policy](alter-encoding-policy.md)
