---
ms.topic: include
ms.date: 02/18/2024
---
[Cribl stream](https://docs.cribl.io/stream/) is a tool that collects and processes machine data and allows you to process machine data into a database for later analysis.

This article shows how to ingest data with Cribl Stream.


## Prerequisites

* An Azure subscription. Create a [free Azure account](https://azure.microsoft.com/free/).
* An Azure Data Explorer [cluster and database](/azure/data-explorer/create-cluster-and-database) with the default cache and retention policies **or** a [KQL database in Microsoft Fabric](/fabric/real-time-analytics/create-database).
* [Azure CLI](/cli/azure/install-azure-cli).
<!--* [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install).-->

## Set up your environment
In this section, you prepare your environment to use Cribl Stream.

## Create a Microsoft Entra service principal

The Microsoft Entra service principal can be created through the [Azure portal](/azure/active-directory/develop/howto-create-service-principal-portal) or programatically, as in the following example.

This service principal will be the identity used by the connector to write data to your table in Kusto. You'll later grant permissions for this service principal to access Kusto resources.

[!INCLUDE [entra-service-principal](../entra-service-principal.md)]

Save your secret key or certificate to authenticate with Cribl Stream.

To create a client secret:  
<!-- need accurate code -->
```azurecli-interactive
az ad app credential reset --id <client_id>
```

To download a certificate:
<!-- NEED info-->

## Create a target table

1. From your query environment, create a table called `SyslogTable` using the following command:

    ```kusto
    .create table SyslogsTable (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```
<!-- need actual columns-->

1. Create the corresponding table mapping `SyslogMapping` for ingested data using the following command:

    ```kusto
    .create table SyslogTable ingestion json mapping "SyslogMapping"
    ```

1. Create an [ingestion batching policy](/azure/data-explorer/kusto/management/batching-policy) on the table for configurable queued ingestion latency.

    > [!TIP]
    > The ingestion batching policy is a performance optimizer and includes three parameters. The first condition satisfied triggers ingestion into the Azure Data Explorer table.

    ```kusto
    .alter table SyslogMapping policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. Use the service principal from [Create a Microsoft Entra service principal](#create-a-microsoft-entra-service-principal) to grant permission to work with the database.

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

> [!NOTE]
> 

## Connect a KQL database to Cribl Stream

## Go to Cribl Stream

### Connect your KQL database to Cribl Stream

To connect Cribl Stream to your kql database do the following:

1. From the top navigation select **Manage**, select a Worker Group.

1. Select **Routing** > **QuickConnect (Stream)** > **Add Destination**. <!-- confirm with Ram that this is how its called> 

1. In the **Set up new QuickConnect Destination** window, choose Azure Data Explorer and then select **Add now**.
:::image type="content" source="../media/ingest-data-cribl/add-azure-data-explorer.png" alt-text="Screenshot of the Set up new QuickConnect Destination window in Cribl with Azure Data Explorer selected."  lightbox="../media/ingest-data-cribl/add-azure-data-explorer.png":::



<!-- from kafka -->
## Run the lab

The following lab is designed to give you the experience of starting to create data, setting up the Cribl Stream connector, and streaming this data to your kql query engine with the connector. You can then look at the ingested data.

### Map the 

### Authenticate 
You can authenticate with Cribl Stream using:

*  Client secret method
*  Client secret method
*  Client secret method



The connector will start queueing ingestion processes to Azure Data Explorer.



## Query and review data

### Confirm data ingestion

1. Wait for data to arrive in the `Storms` table. To confirm the transfer of data, check the row count:

    ```kusto
    Storms | count
    ```

1. Confirm that there are no failures in the ingestion process:

    ```kusto
    .show ingestion failures
    ```

    Once you see data, try out a few queries.

### Query the data

1. To see all the records, run the following [query](/azure/data-explorer/kusto/query/tutorials/learn-common-operators):

    ```kusto
    Syslog
    ```

1. Use `where` and `project` to filter specific data:

    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```

1. Use the [`summarize`](/azure/data-explorer/kusto/query/summarize-operator) operator:

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```

    :::image type="content" source="/azure/data-explorer/includes/media/ingest-data-kafka/kusto-query.png" alt-text="Screenshot of Kafka query column chart results in Azure Data Explorer.":::

For more query examples and guidance, see [Write queries in KQL](/azure/data-explorer/kusto/query/tutorials/learn-common-operators) and [Kusto Query Language documentation](/azure/data-explorer/kusto/query/index).

## Reset

To reset, do the following steps:

1. Stop the containers (`docker-compose down -v`)
1. Delete (`drop table Storms`)
1. Re-create the `Storms` table
1. Recreate table mapping
1. Restart containers (`docker-compose up`)

## Clean up resources

To delete the Azure Data Explorer resources, use [az cluster delete](/cli/azure/kusto/cluster#az-kusto-cluster-delete) or [az Kusto database delete](/cli/azure/kusto/database#az-kusto-database-delete):

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

