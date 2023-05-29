---
title:  .alter-merge table update policy command
description: Learn how to use the `.alter-merge table update policy` command to change the table's update policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge table update policy command

Changes the table's update policy. The [update policy](updatepolicy.md) simplifies the process of syncing and updating data between two tables. When new data is inserted into the source table, a transformation query runs over this data to modify and insert the data into the target table.

> [!NOTE]
>
> * The source table and the table for which the update policy is defined must be in the same database.
> * The update policy function schema and the target table schema must match in their column names, types, and order.
> * If the policy already defined on the table, the PolicyObjects specified in the command are added to the array of PolicyObjects in the existing policy.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` [ *DatabaseName* `.`]*TableName* `policy` `update` *ArrayOfPolicyObjects*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *DatabaseName* | string | | The name of the database. When you run the command from the database context that contains the table to alter, *DatabaseName* is not required. |
| *TableName* | string | &check; | The name of the table. A wildcard, `*`, denotes all tables.|
| *ArrayOfPolicyObjects* | string | &check; | A serialized array of policy objects. For more information, see [update policy](updatepolicy.md).|

## Returns

Returns a JSON representation of the policy.

## Example

The following command changes the update policy for a table using [multi-line string literals](../query/scalar-data-types/string.md#multi-line-string-literals).

````kusto
.alter-merge table MyDatabase.MyTable policy update
```
[
    {
        "IsEnabled": true,
        "Source": "MyTableX",
        "Query": "MyOtherTable",
        "IsTransactional": true,
        "PropagateIngestionProperties": false
    }
]
```
````
