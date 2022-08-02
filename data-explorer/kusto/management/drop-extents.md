---
title: .drop extents - Azure Data Explorer
description: This article describes the drop extents command in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 08/02/2022
---
# .drop extents

Drops extents from a specified database or table.

This command has several variants: In one, the extents to be dropped are specified by a Kusto query. In the other variants, extents are specified using a mini-language described below.

Requires [Table admin permission](../management/access-control/role-based-authorization.md) for each table that has extents returned by the provided query.

> [!NOTE]
> Data shards are called **extents** in Kusto, and all commands use "extent" or "extents" as a synonym.
> For more information on extents, see [Extents (Data Shards) Overview](extents-overview.md).

## Drop extents with a query

Drop extents that are specified using a Kusto query.
A recordset with a column called "ExtentId" is returned.

If `whatif` is used, will just report them, without actually dropping.

### Syntax

`.drop` `extents` [`whatif`] <| *query*

## Drop a specific extent

Requires [Table admin permission](../management/access-control/role-based-authorization.md) if table name is specified.

Requires [Database admin permission](../management/access-control/role-based-authorization.md) if table name isn't specified.

### Syntax

`.drop` `extent` *ExtentId* [`from` *TableName*]

## Drop specific multiple extents

Requires [Table admin permission](../management/access-control/role-based-authorization.md) if table name is specified.

Requires [Database admin permission](../management/access-control/role-based-authorization.md) if table name isn't specified.

### Syntax

`.drop` `extents` `(`*ExtentId1*`,`...*ExtentIdN*`)` [`from` *TableName*]

## Drop extents by specified properties

The command supports emulation mode that produces an output as if the command would have run, but without actually executing it. Use `.drop-pretend` instead of `.drop`.

Requires [Table admin permission](../management/access-control/role-based-authorization.md) if table name is specified.

Requires [Database admin permission](../management/access-control/role-based-authorization.md) if table name isn't specified.

### Syntax

`.drop` `extents` [`older` *N* (`days` | `hours`)] `from` (*TableName* | `all` `tables`) [`trim` `by` (`extentsize` | `datasize`) *N* (`MB` | `GB` | `bytes`)] [`limit` *LimitCount*]

* `older`: Only extents older than *N* days/hours will be dropped.
* `trim`: The operation will trim the data in the database until the sum of extents matches the required size (MaxSize).
* `limit`: The operation will be applied to first *LimitCount* extents.

## Examples

### Drop a specific extent

Use an Extent ID to drop a specific extent.

```kusto
.drop extent 609ad1e2-5b1c-4b79-90c0-1dec262e9f46 from Fruit
```

### Drop multiple specific extents

Use a list of Extent Ids to drop multiple extents.

```kusto
.drop extents (609ad1e2-5b1c-4b79-90c0-1dec262e9f46, 310a60c6-8529-4cdf-a309-fe6aa7857e1d) from Fruit
```

### Remove all extents by time created

Remove all extents created more than 10 days ago, from all tables in database `MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### Remove some extents by time created

Remove all extents in tables `Table1` and `Table2` whose creation time was over 10 days ago

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### Emulation mode: Show which extents would be removed by the command

>[!NOTE]
>Extent ID parameter isn't applicable for this command.

```kusto
.drop-pretend extents older 10 days from all tables
```

### Remove all extents from 'TestTable'

```kusto
.drop extents from TestTable
```

## Return output

|Output parameter |Type |Description |
|---|---|---|
|ExtentId |String |ExtentId that was dropped because of the command
|TableName |String |Table name, where extent belonged  
|CreatedOn |DateTime |Timestamp that holds information about when the extent was initially created |

## Sample output

|Extent ID |Table Name |Created On |
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 |
