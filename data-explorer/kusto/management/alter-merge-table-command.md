---
title: .alter-merge table - Azure Data Explorer
description: This article describes the .alter-merge table command.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 02/21/2023
---
# .alter-merge table

The `.alter-merge table` command:

* Secures data in existing columns
* Adds new columns, `docstring`, and folder to an existing table
* Must run in the context of a specific database that scopes the table name

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` *TableName* (*columnName*:*columnType*[, ...])  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *TableName* | string | &check; | The name of the table to alter. |
| *columnName*:*columnType* | string | &check; | The name of an existing or new column mapped to the type of data in that column. The list of these mappings defines the output column schema.|
| *Documentation* | string | | Free text describing the entity to be added. This string is presented in various UX settings next to the entity names. |
| *FolderName* | string | | The name of the folder to add to the table. |

> [!NOTE]
> If you try to alter a column type, the command will fail. Use [`.alter column`](alter-column.md) instead.

> [!TIP]
> Use `.show table [TableName] cslschema` to get the existing column schema before you alter it.

## How the command affects the data

* Existing data won't be modified or deleted
* New columns will be added to the end of the schema
* Data in new columns is assumed to be null

## Examples

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```

## See also

Use the `.alter` table command when you wish to further redefine the table settings. For more information, see [.alter table](../management/alter-table-command.md).
