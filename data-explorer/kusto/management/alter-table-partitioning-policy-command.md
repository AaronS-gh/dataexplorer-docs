---
title:  .alter table partitioning policy command
description: Learn how to use the .alter table partitioning policy command to change the table's partitioning policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter table partitioning policy

Change's the table's [partitioning policy](partitioningpolicy.md). The partitioning policy defines if and how [extents (data shards)](../management/extents-overview.md) should be partitioned for a specific table or a [materialized view](materialized-views/materialized-view-overview.md).

## Permissions

You must have at least [Database Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter` `table` *TableName* `policy` `partitioning` *PolicyObject*

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *TableName* | string | &check; | The name of the table to alter. |
| *PolicyObject* | string | &check; | A serialized JSON policy object. See [partitioning policy](partitioningpolicy.md). |

### Examples

Set a policy with a hash partition key:

~~~kusto
.alter table [table_name] policy partitioning ```
{
  "PartitionKeys": [
    {
      "ColumnName": "my_string_column",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 128,
        "PartitionAssignmentMode": "Uniform"
      }
    }
  ]
}```
~~~

Set a policy with a uniform range datetime partition key:

~~~kusto
.alter table [table_name] policy partitioning ```
{
  "PartitionKeys": [
    {
      "ColumnName": "my_datetime_column",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}```
~~~

Set a policy with two kinds of partition keys:

~~~kusto
.alter table [table_name] policy partitioning ```
{
  "PartitionKeys": [
    {
      "ColumnName": "my_string_column",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 128,
        "PartitionAssignmentMode": "Uniform"
      }
    },
    {
      "ColumnName": "my_datetime_column",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}```
~~~
