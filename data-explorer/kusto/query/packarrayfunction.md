---
title: pack_array() - Azure Data Explorer
description: Learn how to use the pack_array() function to pack all input values into a dynamic array.
ms.reviewer: alexans
ms.topic: reference
ms.date: 01/05/2023
---
# pack_array()

Packs all input values into a dynamic array.

## Syntax

`pack_array(`*Expr1*`[`,` *Expr2*]`)`

`pack_array(*)`

## Arguments

* *Expr1...N*: Input expressions to be packed into a dynamic array.
* *The wildcard `*`*: providing the wildcard `*` will pack all input columns into a dynamic array.

## Returns

Dynamic array that includes the values of Expr1, Expr2, ... , ExprN.

## Example

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

**Output**

|Column1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

**Output**

|Column1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|
