---
title: .delete table policy ingestiontime command
description: Learn how to use the `.delete table policy ingestiontime` command to delete a table's ingestion time policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 06/13/2023
---
# .delete table policy ingestiontime command

Delete a table's [ingestion time policy](ingestiontimepolicy.md). The policy adds a hidden `datetime` column in the table, called `$IngestionTime`. Whenever new data is ingested, the time of ingestion is recorded in the hidden column.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.delete` `table` *TableName* `policy` `ingestiontime`

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table for which to delete the ingestion time policy.|

### Example

The following command deletes the ingestion time policy for a table named `MyTable`.

```kusto
.delete table `MyTable` policy ingestiontime 
```
