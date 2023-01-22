---
title: search operator - Azure Data Explorer
description: This article describes search operators in Azure Data Explorer.
ms.reviewer: alexans
ms.topic: reference
ms.date: 01/22/2023
---
# search operator

Searches a text pattern in multiple tables and columns.

> [!NOTE]
> It's better to use the [union](unionoperator.md) and [where](whereoperator.md) operators when you know the specific tables and columns you want to search. Searching using the `search` operator can be slow when you have a lot of tables and columns, and a large amount of data to look through.

## Syntax

[*T* `|`] `search` [`kind=` *CaseSensitivity* ] [`in` `(`*TableSources*`)`] *SearchPredicate*

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *T* | string | | The tabular data source to be searched over, such as a table name, a [union operator](unionoperator.md), or the results of a tabular query. Cannot appear together with *TableSources*.|
| *CaseSensitivity* | string | | A flag that controls the behavior of all `string` scalar operators, such as `has`, with respect to case sensitivity. Valid values are `default`, `case_insensitive`, `case_sensitive`. The options `default` and `case_insensitive` are synonymous, since the default behavior is case insensitive.|
| *TableSources* | string | | A comma-separated list of "wildcarded" table names to take part in the search. The list has the same syntax as the list of the [union operator](unionoperator.md). Cannot appear together with *TabularSource*.|
| *SearchPredicate* | string | &check; | A boolean expression to be evaluated for every record in the input. If it returns `true`, the record is outputted. See [Search predicate syntax](#search-predicate-syntax).|

### Search predicate syntax

The *SearchPredicate* allows you to search for specific terms in all columns of a table. The operator that will be applied to a search term depends on the presence and placement of a wildcard asterisk (`*`) in the term, as shown in the following table.

|Literal   |Operator   |
|----------|-----------|
|`billg`   |`has`      |
|`*billg`  |`hassuffix`|
|`billg*`  |`hasprefix`|
|`*billg*` |`contains` |
|`bi*lg`   |`matches regex`|

You can also restrict the search to a specific column, look for an exact match instead of a term match, or search by regular expression. The syntax for each of these cases is shown in the following table.

|Syntax|Explanation|
|--|--|
|*ColumnName*`:`*StringLiteral* | This syntax can be used to restrict the search to a specific column. The default behavior is to search all columns. |
|*ColumnName*`==`*StringLiteral* | This syntax can be used to search for exact matches of a column against a string value. The default behavior is to look for a term-match.|
| *Column* `matches regex` *StringLiteral* | This syntax indicates regular expression matching, in which *StringLiteral* is the regex pattern.|

Use boolean expressions to combine conditions and create more complex searches. For example, `"error" and x==123` would result in a search for records that have the term `error` in any columns and the value `123` in the `x` column.

> [!NOTE]
> If both *TabularSource* and *TableSources* are omitted, the search is carried over all unrestricted tables and views of the database in scope.

### Search predicate syntax examples

  |# |Syntax                                 |Meaning (equivalent `where`)           |Comments|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) "err"`           |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |All string comparisons are case-sensitive|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## Remarks

Unlike the [find operator](findoperator.md), the `search` operator does not support the following:

1. `withsource=`: The output will always include a column called `$table` of type `string` whose value
   is the table name from which each record was retrieved (or some system-generated name if the source
   is not a table but a composite expression).
2. `project=`, `project-smart`: The output schema is equivalent to `project-smart` output schema.

## Examples

### Global term search

Search for a term over all unrestricted tables and views of the database in scope.

```kusto
search "billg"
```

### Conditional global term search

Search for records that match both terms over all unrestricted tables and views of the database in scope.

```kusto
search "billg" and ("steveb" or "satyan")
```

### Search a specific table

Search only in the TraceEvent table.

```kusto
search in (TraceEvent) "billg"
```

### Case-sensitive search

Search for records that match both case-sensitive terms over all unrestricted tables and views of the database in scope.

```kusto
search kind=case_sensitive "BillB" and ("SteveB" or "SatyaN")
```

### Search specific columns

Search for a term in the "CEO" and "CSA" columns over all unrestricted tables and views of the database in scope.

```kusto
search CEO:"billg" or CSA:"billg"
```

### Limit search by timestamp

Search for a term over all unrestricted tables and views of the database in scope if the term appears in a record with a Timestamp greater than the given date.

```kusto
search "billg" and Timestamp >= datetime(1981-01-01)
```

### Searches over all the higher-ups

```kusto
search in (C*, TF) "billg" or "davec" or "steveb"
```

## Performance Tips

|#|Tip|Prefer|Over|
|--|--|--|--|
| 1| Prefer to use a single `search` operator over several consecutive `search` operators|`search "billg" and ("steveb" or "satyan")` |<code>search "billg" &#124; search "steveb" or "satyan"<code>|
| 2| Prefer to filter inside the `search` operator |`search "billg" and "steveb"` |<code>search * &#124; where * has "billg" and * has "steveb"<code> |
