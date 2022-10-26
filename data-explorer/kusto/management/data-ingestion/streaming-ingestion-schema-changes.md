---
title: Streaming ingestion and schema changes - Azure Data Explorer
description: This article discusses options of handling schema changes with streaming ingestion in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 05/20/2020
---
# Streaming ingestion and schema changes

## Background

Cluster nodes cache schema of databases that receive data via streaming ingestion. This process optimizes performance and utilization of cluster resources, but can cause propagation delays when schema changes.

Examples of schema changes are:

* Creation and deletion of databases and tables
* Adding, removing, retyping, or renaming the columns of the table
* Adding or removing pre-created ingestion mappings
* Adding, removing, or altering policies

If schema changes and streaming ingestion flows are uncoordinated, some of the streaming ingestion requests may fail. The failures could include schema-related errors, or the insertion of incomplete or distorted data into the table.
When implementing custom ingestion application it is highly recommended to handle the schema-related failures by performing retries for a limited time, or by rerouting data from the failed requests via queued ingestion methods.

## Clearing the schema cache

Reduce the effects of propagation delay by explicitly clearing the schema cache on the cluster nodes.
Clear the schema cache using one of the [Clear schema cache for streaming ingestion](clear-schema-cache-command.md) management commands.
If the streaming ingestion flow and schema changes are coordinated, you can completely eliminate failures and their associated data distortion. 

**Coordinated flow example:**

1. Suspend streaming ingestion.
1. Wait until all outstanding streaming ingestion requests are complete>
1. Do schema changes.
1. Issue one or several `.clear cache streaming ingestion` schema commands. 
    * Repeat until successful and all rows in the command output indicate success
1. Resume streaming ingestion.

> [!NOTE]
> Using clear cache streaming ingestion schema commands frequently may have an adverse effect on the performance of streaming ingestion.
