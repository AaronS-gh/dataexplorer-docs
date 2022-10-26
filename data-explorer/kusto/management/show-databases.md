---
title: .show databases - Azure Data Explorer
description: This article describes .show databases in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 02/13/2020
---
# .show databases

Returns a table in which every record corresponds to a database in the cluster that the user has access to.

For a table that shows the properties of the context database, see [`.show database`](show-database.md).

**Syntax**

`.show` `databases`

**Output schema**

|Column name       |Column type|Description                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |The name of the database attached to the cluster                    |
|PersistentStorage |`string`   |The persistent storage "root" of the database. For internal use only          |
|Version           |`string`   |The version of the database. For internal use only                       |
|IsCurrent         |`bool`     |Whether this database is the database context of the request                    |
|DatabaseAccessMode|`string`   |One of `ReadWrite`, `ReadOnly`, `ReadOnlyFollowing`, or `ReadWriteEphemeral`    |
|PrettyName        |`string`   |The pretty name of the database, if any                        |
|ReservedSlot1     |`bool`     |Reserved. For internal use only              |
|DatabaseId        |`guid`     |A globally unique identifier for the database. For internal use only          |
