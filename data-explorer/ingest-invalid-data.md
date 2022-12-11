---
title: How invalid data affects ingestion in Azure Data Explorer.
description: Learn about possible outcomes of ingesting invalid data in Azure Data Explorer.
ms.reviewer: slneimer
ms.topic: conceptual
ms.date: 11/14/2022
---

# Azure Data Explorer ingestion behavior on invalid data

Data that is malformed, unparsable, too large, or doesn't conform to the schema may fail to be ingested properly. The following tables describe what to expect when ingesting invalid data into Azure Data Explorer.

> For more information about why ingestion might fail, see [Ingestion failures](kusto/management/ingestionfailures.md) and  [Ingestion error codes in Azure Data Explorer](error-codes.md).

## Failure with error code

The following table shows cases where ingestion of invalid data fails with an error code:

|Case                                                                                           |ErrorCode                          |
|-----------------------------------------------------------------------------------------------|-----------------------------------|
|Invalid or corrupted format (actual data does not match the specified format)                  |BadRequest_InvalidBlob             |
|Empty data (Engine V2)                                                                         |BadRequest_InvalidBlob             |
|Empty data (Engine V3)                                                                         |BadRequest_NoRecordsOrWrongFormat  |
|Malformed records in JSON data ingested with format="multijson" (e.g. missing braces or quotes)|BadRequest_InvalidBlob             |
|CSV/JSON record larger than 64MB (Engine V2 only)                                              |Stream_InputStreamTooLarge         |
|CSV lines with inconsistent number of fields                                                   |Stream_WrongNumberOfFields         |

## Failure without error code

The following table shows cases where ingestion succeeds without an error, silently handling the invalid data:

|Case                                                                                                                       |Notes                                         |
|---------------------------------------------------------------------------------------------------------------------------|----------------------------------------------|
|Malformed records in JSON data ingested with format="json". For example: unexpected newlines, missing braces or quotes.            |Malformed records are ignored and not ingested|
|Value larger than 1MB ingested into a string column                                                                        |Value truncated up to 1MB                     |
|Value larger than 1MB (default, see [Encoding policy](kusto/management/encoding-policy.md)) ingested into a dynamic column |NULL value filled                             |
|Value not matching the table schema data type. For example: floating point value ingested into an `int` column.                      |NULL value filled                             |
|Mapped fields are missing from the data                                                                                    |NULL value filled                             |

## See

* [Data ingestion](ingest-data-overview.md)
* [Ingestion failures](kusto/management/ingestionfailures.md)
* [Encoding policy](kusto/management/encoding-policy.md)
