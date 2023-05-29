---
title:  .alter table ingestion batching policy command
description: Learn how to use the `.alter table ingestion batching policy` command to set the table's ingestion batching policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter table ingestion batching policy command

Sets the table's [ingestion batching policy](batchingpolicy.md) to determine when data aggregation stops and a batch is sealed and ingested.

If the policy isn't set for a table, the database-level policy applies. If it isn't set as well, the [default values](batchingpolicy.md#defaults-and-limits) apply.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Defaults and limits

See [defaults and limits](batchingpolicy.md#defaults-and-limits).

## Syntax

`.alter` `table` [ *DatabaseName*`.`]*TableName* `policy` `ingestionbatching` *PolicyObject*

`.alter` `tables` `(`*Table1* `,` *Table2*  [`,`...]`)` `policy` `ingestionbatching` *PolicyObject*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *TableName* | string | &check; | The name of the table to alter.|
| *DatabaseName* | string | | The name of the database. When you run the command from the database context that contains the table to alter, *DatabaseName* is not required.|
| *PolicyObject* |string|&check;| A serialized JSON policy object. See [ingestion batching policy](batchingpolicy.md).|

## Examples

The following command sets a batch ingress data time of 30 seconds, for 500 files, or 1 GB, whichever comes first.

````kusto
.alter table MyDatabase.MyTable policy ingestionbatching
```
{
    "MaximumBatchingTimeSpan" : "00:00:30",
    "MaximumNumberOfItems" : 500,
    "MaximumRawDataSizeMB": 1024
}
```
````

The following command sets a batch ingress data time of 1 minute, for 20 files, or 300 MB, whichever comes first.

````kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching
```
{
    "MaximumBatchingTimeSpan" : "00:01:00",
    "MaximumNumberOfItems" : 20,
    "MaximumRawDataSizeMB": 300
}
```
````

>[!NOTE] 
> If you don't specify all parameters of a *PolicyObject*, the unspecified parameters will be set to (default values)[https://learn.microsoft.com/en-us/azure/data-explorer/kusto/management/batchingpolicy#sealing-a-batch]. For example, specifying only "MaximumBatchingTimeSpan" will result in "MaximumNumberOfItems" and "MaximumRawDataSizeMB" being set to default.

## Next steps

* [alter database batching policy](alter-database-ingestion-batching-policy.md)
