---
title: Add a cluster connection in the Azure Data Explorer web UI
description: This article describes how to add a cluster connection in the Azure Data Explorer web UI.
ms.reviewer: mibar
ms.topic: reference
ms.date: 07/19/2023
---

# Add a cluster connection in the Azure Data Explorer web UI

This article describes how to add a connection to an Azure Data Explorer cluster in the [Azure Data Explorer web UI](https://dataexplorer.azure.com/).

When you add a cluster connection, you associate a specific user account and Azure Active Directory (Azure AD) directory with the connection. The cluster establishes the connection and runs queries using the provided credentials.

This capability is especially valuable for users who manage multiple clusters across different user accounts or Azure AD directories. It eliminates the need for repetitive signing out and signing back in to access clusters associated with different sets of credentials. Instead of managing multiple logins, you can switch between and view clusters associated with various accounts and directories within a unified pane.

## Prerequisites

* A Microsoft account or an Azure Active Directory user identity. An Azure subscription isn't required.
* Sign-in to the [Azure Data Explorer web UI](https://dataexplorer.azure.com/).

## Add a cluster connection

To add a connection to your Azure Data Explorer cluster, do the following:

1. From the main left menu, select **Query**.

    :::image type="content" source="media/web-ui-add-cluster/query-widget.png" alt-text="Screenshot of the query widget in the main menu of the web UI." lightbox="media/web-ui-add-cluster/query-widget.png":::

1. In the upper left corner, select **Add**. From the dropdown menu, select **Connection**.

1. In the **Add connection** dialog box, enter the cluster **Connection URI** and **Display name**. To find the connection URI, go to your cluster resource in the [Azure portal](https://ms.portal.azure.com/). The connection URI is the **URI** found in the **Overview**. To add a free sample cluster, specify "help" as the **Connection URI**.

    :::image type="content" source="media/web-ui-add-cluster/add-connection-dialog.png" alt-text="Screenshot of add cluster connection dialog box." lightbox="media/web-ui-add-cluster/add-connection-dialog.png":::

1. If the displayed user is not the intended user, select **Connect as another user** and proceed to add or select the appropriate account.

1. In case your user is linked to multiple Azure AD directories, select **Switch directory** to access a dropdown menu listing all directories associated with the current account. Choose the relevant directory for this specific cluster connection.

1. Select **Add** to add the connection. Your cluster and databases should now be visible in the left panel. For example, the following image shows the `help` cluster connection.

    :::image type="content" source="media/web-ui-add-cluster/help-cluster-web-ui.png" alt-text="Screenshot of the help cluster and databases." lightbox="media/web-ui-add-cluster/help-cluster-web-ui.png":::

## Next steps

* Get data with the [ingestion wizard](ingest-data-wizard.md)
* [Write Kusto Query Language queries in the web UI](web-ui-kql.md)
* [Tutorial: Learn common Kusto Query Language operators](kusto/query/tutorials/learn-common-operators.md)
