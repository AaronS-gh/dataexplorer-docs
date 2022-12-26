---
title: series_dot_product() - Azure Data Explorer
description: This article describes series_dot_product() in Azure Data Explorer.
ms.reviewer: adieldar
ms.topic: reference
ms.date: 12/12/2022
---
# series_dot_product()

Calculates the dot product of two numeric series.

The function `series_dot_product()` takes two numeric series as input, and calculates their [dot product](https://en.wikipedia.org/wiki/Dot_product).

## Syntax

`series_dot_product(`*series1*`,` *series2*`)`

**Alternate Syntax**

`series_dot_product(`*series*`,` *numeric*`)`

The alternate syntax describes that one of the function parameters can be a numerical scalar.
In that case, the numerical scalar will be broadcasted to a vector whose length is the other series containing that scalar.

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *series1, series2* | dynamic arrays |  &check | Input arrays with numeric data, to be element-wise multiplied and then summed into a double type value.

## Returns

Returns a value of type `real` whose value is the sum over the product of each element of *series1* with the corresponding element of *series2*.
In case both series length isn't equal, the longer series will be truncated to the length of the shorter one.
Any non-numeric element of the input series will be ignored.

> [!NOTE]
> If one or both input arrays are empty, the result will be `null`.

## Example

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 3 step 1 
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_dot_product_s2 = series_dot_product(s1, s2)
```

|s1|s2|s1_dot_product_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|12|
|[2,4,8]|[8,4,2]|48|
|[3,6,12]|[12,6,3]|108|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 3 step 1 
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = x
| extend s1_dot_product_s2 = series_dot_product(s1, s2)
```

|s1|s2|s1_dot_product_s2|
|---|---|---|
|[1,2,4]|1|7|
|[2,4,8]|2|28|
|[3,6,12]|3|63|
