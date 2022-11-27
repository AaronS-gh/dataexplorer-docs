---
title: .alter-merge table - Azure Data Explorer
description: This article describes the .alter-merge table command.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 08/05/2021
---
# .alter-merge table

The `.alter-merge table` command:

* Secures data in existing columns
* Adds new columns, `docstring`, and folder, to an existing table
* Must run in the context of a specific database that scopes the table name
* Requires [Table Admin permission](../management/access-control/role-based-authorization.md)

`DocString` is free text that you can attach to a table/function/column describing the entity. This string is presented in various UX settings next to the entity names.

> [!WARNING]
> If you change the type of a column during `.alter-merge`, all values in that column will be nullified. This may lead to unexpected data loss.

> [!TIP]
> The `.alter-merge` has a counterpart, the `.alter` table command that has similar functionality. For more information, see [`.alter table`](../management/alter-table-command.md)

**Syntax**

`.alter-merge` `table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]

Specify the table columns:
 * Columns that don't exist and which you specify, are added at the end of the existing schema.
 * If the passed schema doesn't contain some table columns, the columns won't be deleted.
 * If you specify an existing column with a different type, the command will fail.

> [!TIP]
> Use `.show table [TableName] cslschema` to get the existing column schema before you alter it.

How will the command affect the data?
* Existing data isn't physically modified by the command. Data in removed columns is ignored. Data in new columns is assumed to be null.
* Depending on how the cluster is configured, data ingestion might modify the table's column schema, even without user interaction. When you make changes to a table's column schema, ensure that ingestion won't add needed columns that the command will then remove.

**Examples**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
