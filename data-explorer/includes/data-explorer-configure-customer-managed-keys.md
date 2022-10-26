---
ms.topic: include
ms.date: 01/07/2020
---

Azure Data Explorer encrypts all data in a storage account at rest. By default, data is encrypted with Microsoft-managed keys. For additional control over encryption keys, you can supply customer-managed keys to use for data encryption. 

Customer-managed keys must be stored in an [Azure Key Vault](/azure/key-vault/key-vault-overview). You can create your own keys and store them in a key vault, or you can use an Azure Key Vault API to generate keys. The Azure Data Explorer cluster and the key vault must be in the same region, but they can be in different subscriptions. For a detailed explanation on customer-managed keys, see [customer-managed keys with Azure Key Vault](/azure/storage/common/storage-service-encryption). 

This article shows you how to configure customer-managed keys.

## Configure Azure Key Vault

To configure customer-managed keys with Azure Data Explorer, you must [set two properties on the key vault](/azure/key-vault/key-vault-ovw-soft-delete): **Soft Delete** and **Do Not Purge**. These properties aren't enabled by default. To enable these properties, perform **Enabling soft-delete** and **Enabling Purge Protection** in [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) or [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) on a new or existing key vault. Only RSA keys of size 2048 are supported. For more information about keys, see [Key Vault keys](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Data encryption using customer managed keys is not supported on [leader and follower clusters](../follower.md)

## Assign an identity to the cluster

To enable customer-managed keys for your cluster, first assign either a system-assigned or user-assigned managed identity to the cluster. You'll use this managed identity to grant the cluster permissions to access the key vault. To configure managed identities, see [managed identities](../configure-managed-identities-cluster.md).