---
title: Solution architectures in Azure
description: Learn about solution architectures in Azure Data Explorer.
ms.reviewer: 
ms.topic: reference
ms.date: 07/05/2022
---
# Solution architectures

Azure Data Explorer is a fully managed, high-performance, big data analytics platform that makes it easy to analyze high volumes of data in near real time. Azure Data Explorer can be used with other Azure offerings to create end-to-end solutions for various use cases.

Use the [Azure Architecture Center](/azure/architecture/) to find architectures suitable for your needs. The Azure Architecture Center provides reusable patterns for building architectures and sample customer stories that can help you make decisions about how best to use Azure services.

This document refers you to all architectures that include Azure Data Explorer.

## Big data analytics with Azure Data Explorer

Azure Data Explorer and Azure Synapse Analytics work together for near real-time analytics and modern data warehousing use cases.

> [!div class="nextstepaction"]
> [Big data analytics with Azure Data Explorer](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer)

## Azure Data Explorer monitoring

Hybrid end-to-end monitoring solution integrated with Microsoft Sentinel and Azure Monitor for ingesting streamed and batched logs from diverse sources, on-premises, or any cloud, within an enterprise ecosystem.

> [!div class="nextstepaction"]
> [Azure Data Explorer monitoring](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)

## Azure Data Explorer interactive analytics

Interactive analytics with Azure Data Explorer to explore data with improvised, interactive, and lightning fast queries over small to large volumes of data. This data exploration can be done using native Azure Data Explorer tools or alternative tools of your choice. This solution focuses on the integration of Azure Data Explorer with rest of the data platform ecosystem.

> [!div class="nextstepaction"]
> [Azure Data Explorer interactive analytics](/azure/architecture/solution-ideas/articles/interactive-azure-data-explorer)

## Long-term security log retention with Azure Data Explorer

This article outlines a solution for long-term retention of security logs. At the core of the architecture is Azure Data Explorer. This service provides storage for security data at minimal cost but keeps that data in a format that you can query. Other main components include Log Analytics, Microsoft Defender for Endpoint, and Microsoft Sentinel.

> [!div class="nextstepaction"]
> [Long-term security log retention with Azure Data Explorer](/azure/architecture/example-scenario/security/security-log-retention-azure-data-explorer)

## IoT analytics with Azure Data Explorer

Near real-time analytics over fast flowing, high volume streaming data from IoT devices, sensors, connected buildings and vehicles, and so on. It focuses on integration of Azure Data Explorer with other IoT services to cater to both operational and analytical workloads using Cosmos DB and Azure Data Explorer.

> [!div class="nextstepaction"]
> [IoT analytics with Azure Data Explorer](/azure/architecture/solution-ideas/articles/iot-azure-data-explorer)

## Geospatial data processing and analytics

Solution for making large volumes of geospatial data available for analytics. The approach is based on Advanced Analytics Reference Architecture and uses these Azure services:

- Azure Databricks with GIS Spark libraries processes data.
- Azure Database for PostgreSQL queries data that users request through APIs.
- Azure Data Explorer runs fast exploratory queries.
- Azure Maps creates visuals of geospatial data in web applications.
- The Azure Maps Power BI visual feature of Power BI provides customized reports.

> [!div class="nextstepaction"]
> [Geospatial data processing and analytics](/azure/architecture/example-scenario/data/geospatial-data-processing-analytics-azure)

## Long-term security log retention with Azure Data Explorer

Solution for long-term retention of security logs. At the core of the architecture is Azure Data Explorer. This service provides storage for security data at minimal cost but keeps that data in a format that you can query. Other main components include:

* Microsoft Defender for Endpoint and Microsoft Sentinel, for these capabilities:
    * Comprehensive endpoint security
    * Security information and event management (SIEM)
    * Security orchestration automated response (SOAR)
* Log Analytics, for short-term storage of Sentinel security logs.

> [!div class="nextstepaction"]
> [Long-term security log retention with Azure Data Explorer](/azure/architecture/example-scenario/security/security-log-retention-azure-data-explorer)

## Content Delivery Network analytics

Low-latency high throughput ingestion for large volumes of Content Delivery Network (CDN) logs for building near real-time analytics dashboards.

This solution also uses the following services:

* Azure Data Lake Storage
* Logic Apps
* Dashboarding

> [!div class="nextstepaction"]
> [Content Delivery Network analytics](/azure/architecture/solution-ideas/articles/content-delivery-network-azure-data-explorer)
