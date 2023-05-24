---
title:  Alter encoding policy
description: Learn how to use the .alter encoding policy command to change the encoding policy.
ms.reviewer: alexans
ms.topic: reference
ms.date: 04/20/2023
---
# .alter encoding policy

Alters the encoding policy. For an overview of the encoding policy, see [Encoding policy](encoding-policy.md).

> [!NOTE]
> Encoding policy changes do not affect data that has already been ingested.
> Only new ingestion operations will be performed according to the new policy.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter column` *EntityIdentifier* `policy` `encoding` [`type` `=` *EncodingPolicyType*]

> [!NOTE]
> If you omit the `type`, the existing encoding policy profile is cleared reset to the default value.

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*EntityIdentifier*|string|&check;|The identifier for the column.|
|*EncodingPolicyType*|string||The type of the encoding policy to apply to the specified column. See [encoding policy types](#encoding-policy-types) for the possible values.|

### Encoding policy types

The following table contains the possible values for the *EncodingPolicyType* parameter.

|Encoding Policy Profile | Description |
|------------------------|------------|
|`Identifier`            | Suitable for columns that have data that represents ID-like information (for example, guids). This policy applies the required index for this column to gain both query performance and reduce size in the storage. |
|`BigObject`             | Suitable for columns of dynamic type, which holds large objects. For example, the output of [hll aggregate function](../query/hll-aggfunction.md)). This policy disables the index of this column and overrides `MaxValueSize` property in the encoding Policy to 2 MB. |
|`BigObject32`           | Similar to `BigObject` in terms of target scenarios. Overrides `MaxValueSize` property in the encoding Policy to 32 MB. |
|`Null`                  | Sets the current default encoding policy to the column and clears the previous encoding policy profile.                               |

## Example

```kusto
.alter column Logs.ActivityId policy encoding type='identifier'
```

## See also

* [Encoding policy](encoding-policy.md)
* [.show encoding policy](show-encoding-policy.md)
