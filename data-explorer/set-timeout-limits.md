---
title: Set timeouts in Azure Data Explorer
description: Learn how to set the query timeout length in various Azure Data Explorer tools, such as Kusto.Explorer and the Azure Data Explorer web UI.
ms.topic: how-to
ms.date: 01/09/2023
---

# Set timeout limits

In Azure Data Explorer, it's possible to customize the timeout length for your queries and [management commands](kusto/management/index.md). Adjusting the timeout can help you optimize the performance and efficiency of your queries and commands.

In this guide, you'll learn how to set a custom timeout in various tools such as the [Azure Data Explorer web UI](web-ui-overview.md), [Kusto.Explorer](kusto/tools/kusto-explorer.md), [Kusto.Cli](/kusto/tools/kusto-cli.md), [Power BI](power-bi-data-connector.md), and when using an [SDK](#sdk). Certain tools have their own default timeout values, but it may be helpful to adjust these values based on the complexity and expected runtime of your queries.

## Azure Data Explorer web UI

This section will describe how to configure a custom query timeout and admin command timeout in the Azure Data Explorer web UI.

### Prerequisites

* A Microsoft account or an Azure Active Directory user identity. An Azure subscription isn't required.
* An Azure Data Explorer cluster and database. You can [create a free cluster](start-for-free-web-ui.md) or [create a full cluster](create-cluster-database-portal.md). To decide which is best for you, check the [feature comparison](start-for-free.md#feature-comparison).

### Set timeout length

1. Sign in to the [Azure Data Explorer web UI](https://dataexplorer.azure.com/home) with your Microsoft account or Azure Active Directory user identity credentials.

1. In the top menu, select the **Settings** icon.

1. From the left menu, select **Connection**.

1. Under the **Query timeout (in minutes)** setting, use the slider to choose the desired query timeout length.

1. Under the **Admin command timeout (in minutes)** setting, use the slider to choose the desired admin command timeout length.

:::image type="content" source="media/set-timeouts/web-ui-set-timeouts.png" alt-text="Screenshot of the settings in the Azure Data Explorer web UI that control timeout length.":::

1. Close the settings window, and the changes will be saved automatically.

## Kusto.Explorer

This section will describe how to configure a custom query timeout and admin command timeout in the Kusto.Explorer.

### Prerequisites

* Download and install the [Kusto.Explorer tool](tools/../kusto/tools/kusto-explorer.md#installing-kustoexplorer).
* An Azure Data Explorer cluster and database. You can [create a free cluster](start-for-free-web-ui.md) or [create a full cluster](create-cluster-database-portal.md). To decide which is best for you, check the [feature comparison](start-for-free.md#feature-comparison).

### Set timeout length

1. Open the Kusto.Explorer tool.

1. In the top menu, select the **Tools** tab.

1. On the right-hand side, select **Options**.

    :::image type="content" source="media/set-timeouts/kusto-explorer-options-widget.png" alt-text="Screenshot showing the options widget in the Kusto.Explorer tool.":::

1. In the left menu, select **Connections**.

1. In the **Query Server Timeout** setting, enter the desired timeout length. The maximum is 1 hour.

1. Under the **Admin Command Server Timeout** setting, enter the desired timeout length. The maximum is 1 hour.

    :::image type="content" source="media/set-timeouts/kusto-explorer-set-timeouts.png" alt-text="Screenshot showing settings that control the timeout length in Kusto.Explorer.":::

1. Select **OK** to save the changes.

## Kusto.Cli

This section will describe how to configure a custom server timeout in the Kusto.Cli.

### Prerequisites

* Install the [Kusto.Cli](kusto/tools/kusto-cli.md) by downloading the package [Microsoft.Azure.Kusto.Tools](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).

### Set timeout length

Run the following command to set the *servertimeout* [client request property](kusto/api/netfx/request-properties.md#clientrequestproperties-options) with the desired timeout length as a valid [timespan](kusto/query/scalar-data-types/timespan.md) value up to 1 hour.

```dotnet
Kusto.Cli.exe <ConnectionString> -execute:"#crp servertimeout=<timespan>"
```

Alternatively, use the following command to set the *norequesttimeout* [client request property](kusto/api/netfx/request-properties.md#clientrequestproperties-options), which will set the timeout to the maximum value of 1 hour.

```dotnet
Kusto.Cli.exe <ConnectionString> -execute:"#crp norequesttimeout=True"
```

Once set, the client request property applies to all future values until the app is restarted or another value gets set. To retrieve the current value, use:

```dotnet
#crp servertimeout
```

## Power BI

This section will describe how to configure a custom server timeout in Power BI.

### Prerequisites

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/)

### Set timeout length

1. Connect to your Azure Data Explorer cluster from Power BI desktop.

1. In the top menu, select **Transform Data**.

   :::image type="content" source="media/set-timeouts/power-bi-transform-data.png" alt-text="Screenshot of the transform data option in Power BI Desktop.":::

1. In the top menu, select **Advanced Query Editor**.

   :::image type="content" source="media/set-timeouts/power-bi-advanced-editor.png" alt-text="Screenshot of the powerBI advanced query editor option in Power BI Desktop.":::

1. In the pop-up window, set the timeout option in the fourth parameter of the `AzureDataExplorer.Contents` method. The following example shows how to set a timeout length of 59 minutes.

    ```Power Query M
    let 
        Source = AzureDataExplorer.Contents(<cluster>, <database>, <table>, [Timeout=#duration(0,0,59,0)])
    in
        Source
    ```

For more information, see [configuring Azure Data Explorer connector options in an M Query](/power-query/connectors/azuredataexplorer#configuring-azure-data-explorer-connector-options-in-an-m-query).

## SDK

This section will describe how to configure a custom server timeout when using an SDK.

### Prerequisites

* The SDK for your use case: [.NET](kusto/api/netfx/about-the-sdk.md), [Python](kusto/api/python/kusto-python-client-library.md), [R](kusto/api/r/kusto-r-client-library.md), [Java](kusto/api/java/kusto-java-client-library.md), [Node](kusto/api/node/kusto-node-client-library.md), or [Go](kusto/api/golang/kusto-golang-client-library.md)

### Set timeout length

To set timeouts when using an SDK, you'll need to use [client request properties](kusto/api/netfx/request-properties.md).

1. Install and import the necessary libraries or packages for your specific SDK.

1. Create a new `ClientRequestProperties` object or use an existing one.

   1. To set a maximal server allowed timeout, set *norequesttimeout* to true.

   1. To set a specific timeout, set *servertimeout* to a valid [timespan](kusto/query/scalar-data-types/timespan.md) value up to 1 hour.

1. Use the `ClientRequestProperties` object in your query or control-command.

1. The server will cancel the request if it exceeds the defined timeout.
