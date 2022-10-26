---
title: The .show cluster policy request classification command - Azure Data Explorer
description: This article describes the .show cluster policy request classification command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 09/26/2021
---
# .show cluster request classification policy

Shows the cluster's request classification policy. For more information, see [Request classification policy](request-classification-policy.md).

## Syntax

`.show` `cluster` `policy` `request_classification`

## Returns

Returns a JSON representation of the policy.

### Example

Display the cluster's request classification policy.

```kusto
.show cluster policy request_classification
```
