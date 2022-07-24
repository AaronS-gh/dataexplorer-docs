---
title: Ingest from storage using Event Grid subscription - Azure Data Explorer
description: This article describes Ingest from storage using Event Grid subscription in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: how-to
ms.date: 03/15/2022
---
# Event Grid data connection

Event Grid ingestion is a pipeline that listens to Azure storage, and updates Azure Data Explorer to pull information when subscribed events occur. Azure Data Explorer offers continuous ingestion from Azure Storage (Blob storage and ADLSv2) with [Azure Event Grid](/azure/event-grid/overview) subscription for blob created or blob renamed notifications and streaming these notifications to Azure Data Explorer via an Azure Event Hubs.

The Event Grid ingestion pipeline goes through several steps. You create a target table in Azure Data Explorer into which the [data in a particular format](#data-format) will be ingested. Then you create an Event Grid data connection in Azure Data Explorer. The Event Grid data connection needs to know [events routing](#events-routing) information, such as what table to send the data to and the table mapping. You also specify [ingestion properties](#ingestion-properties), which describe the data to be ingested, the target table, and the mapping. You can generate sample data and [upload blobs](#upload-blobs) or [rename blobs](#rename-blobs) to test your connection. [Delete blobs](#delete-blobs-using-storage-lifecycle) after ingestion.

Event Grid ingestion can be managed through the [Azure portal](ingest-data-event-grid.md), using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md).

For general information about data ingestion in Azure Data Explorer, see [Azure Data Explorer data ingestion overview](ingest-data-overview.md).

## Data format

* See [supported formats](ingestion-supported-formats.md).
* See [supported compressions](ingestion-supported-formats.md#supported-data-compression-formats).
    * The original uncompressed data size should be part of the blob metadata, or else Azure Data Explorer will estimate it. The ingestion uncompressed size limit per file is 6 GB.

> [!NOTE]
> Event Grid notification subscription can be set on Azure Storage accounts for `BlobStorage`, `StorageV2`, or [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).

## Ingestion properties

You can specify [ingestion properties](ingestion-properties.md) of the blob ingestion via the blob metadata.
You can set the following properties:

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## Events routing

When you create a data connection to your cluster, you specify the routing for where to send ingested data. The default routing is to the target table specified in the connection string that is associated with the target database. The default routing for your data is also referred to as *static routing*. You can specify an alternative routing for your data by using the event data properties.

### Route event data to an alternate database

Routing data to an alternate database is off by default. To send the data to a different database, you must first set the connection as a multi-database connection. You can do this in the Azure portal [Azure portal](ingest-data-event-grid.md#create-an-event-grid-data-connection), [C#](data-connection-event-grid-csharp.md#add-an-event-grid-data-connection), [Python](data-connection-event-grid-python.md#add-an-event-grid-data-connection), or an [ARM template](data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection). The user, group, service principal, or managed identity used to allow database routing must at least have the **contributor** role and write permissions on the cluster.

To specify an alternate database, set the *Database* [ingestion property](#ingestion-properties).

> [!WARNING]
> Specifying an alternate database without setting the connection as a multi-database data connection will cause the ingestion to fail.

### Route event data to an alternate table

When setting up a blob storage connection to Azure Data Explorer cluster, specify target table properties:

* table name
* data format
* mapping

You can also specify target table properties for each blob, using blob metadata. The data will dynamically route, as specified by [ingestion properties](#ingestion-properties).

The example below shows you how to set ingestion properties on the blob metadata before uploading it. Blobs are routed to different tables.

In addition, you can specify the target database. An Event Grid data connection is created within the context of a specific database. Hence this database is the data connection's default database routing. To send the data to a different database, set the "KustoDatabase" ingestion property and set the data connection as a Multi database data connection.
Routing data to another database is disabled by default (not allowed).
Setting a database ingestion property that is different than the data connection's database, without allowing data routing to multiple databases (setting the connection as a Multi database data connection), will cause the ingestion to fail.

For more information, see [upload blobs](#upload-blobs).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.Metadata.Add("kustoDatabase", "AnotherDB");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## Upload blobs

You can create a blob from a local file, set ingestion properties to the blob metadata, and upload it. For examples, see [Ingest blobs into Azure Data Explorer by subscribing to Event Grid notifications](ingest-data-event-grid.md#generate-sample-data)

> [!NOTE]
>
> * Use `BlockBlob` to generate data. `AppendBlob` is not supported.
> * Using Azure Data Lake Gen2 storage SDK requires using `CreateFile` for uploading files and `Flush` at the end with the close parameter set to "true".
> For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).
> * When the event hub endpoint doesn't acknowledge receipt of an event, Azure Event Grid activates a retry mechanism. If this retry delivery fails, Event Grid can deliver the undelivered events to a storage account using a process of *dead-lettering*. For more information, see [Event Grid message delivery and retry](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

## Rename blobs

When using ADLSv2, you can rename a blob to trigger blob ingestion to Azure Data Explorer. For example, see [Ingest blobs into Azure Data Explorer by subscribing to Event Grid notifications](ingest-data-event-grid.md#generate-sample-data).

> [!NOTE]
>
> * Directory renaming is possible in ADLSv2, but it doesn't trigger *blob renamed* events and ingestion of blobs inside the directory. To ingest blobs following renaming, directly rename the desired blobs.
> * If you defined filters to track specific subjects while [creating the data connection](ingest-data-event-grid.md#create-an-event-grid-data-connection) or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path.

## Delete blobs using storage lifecycle

Azure Data Explorer won't delete the blobs after ingestion. Use [Azure Blob storage lifecycle](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) to manage your blob deletion. It's recommended to keep the blobs for three to five days.

## Known Event Grid issues

* When using Azure Data Explorer to [export](kusto/management/data-export/export-data-to-storage.md) the files used for Event Grid ingestion, note:
    * Event Grid notifications aren't triggered if the connection string provided to the export command or the connection string provided to an [external table](kusto/management/data-export/export-data-to-an-external-table.md) is a connecting string in [ADLS Gen2 format](kusto/api/connection-strings/storage-connection-strings.md#storage-connection-string-templates) (for example, `abfss://filesystem@accountname.dfs.core.windows.net`) but the storage account isn't enabled for hierarchical namespace.
    * If the account isn't enabled for hierarchical namespace, connection string must use the [Blob Storage](kusto/api/connection-strings/storage-connection-strings.md#storage-connection-string-templates) format (for example, `https://accountname.blob.core.windows.net`). The export works as expected even when using the ADLS Gen2 connection string, but notifications won't be triggered and Event Grid ingestion won't work.

## Next steps

* [Ingest blobs into Azure Data Explorer by subscribing to Event Grid notifications](ingest-data-event-grid.md)
* [Create an Event Grid data connection for Azure Data Explorer by using C#](data-connection-event-grid-csharp.md)
* [Create an Event Grid data connection for Azure Data Explorer by using Python](data-connection-event-grid-python.md)
* [Create an Event Grid data connection for Azure Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
* [Use one-click ingestion to create an Event Hubs data connection for Azure Data Explorer](one-click-event-hub.md)
* [Use one-click ingestion to ingest CSV data from a container to a new table in Azure Data Explorer](one-click-ingestion-new-table.md)
