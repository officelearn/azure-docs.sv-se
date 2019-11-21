---
title: Azure Services that support managed identities - Azure AD
description: List of services that support managed identities for Azure resources and Azure AD authentication
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224292"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services that support managed identities for Azure resources

Managed identities for Azure resources provide Azure services with an automatically managed identity in Azure Active Directory. Using a managed identity, you can authenticate to any service that supports Azure AD authentication without having credentials in your code. We are in the process of integrating managed identities for Azure resources and Azure AD authentication across Azure. Check back often for updates.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

The following Azure services support managed identities for Azure resources:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Förhandsversion | Förhandsversion | Förhandsversion | 
| User assigned | Tillgänglig | Förhandsversion | Förhandsversion | Förhandsversion |

Refer to the following list to configure managed identity for Azure Virtual Machines (in regions where available):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Förhandsversion | Förhandsversion | Förhandsversion |
| User assigned | Tillgänglig | Förhandsversion | Förhandsversion | Förhandsversion |

Refer to the following list to configure managed identity for Azure Virtual Machine Scale Sets (in regions where available):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| User assigned | Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure App Service (in regions where available):

- [Azure-portalen](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Tillgänglig | Inte tillgänglig | Inte tillgänglig |
| User assigned | Tillgänglig | Tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Managed identity type |All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| User assigned | Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure Functions (in regions where available):

- [Azure-portalen](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Förhandsversion | Förhandsversion | Inte tillgänglig | Förhandsversion |
| User assigned | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure Logic Apps (in regions where available):

- [Azure-portalen](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| User assigned | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure Data Factory V2 (in regions where available):

- [Azure-portalen](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Tillgänglig | Inte tillgänglig | Inte tillgänglig |
| User assigned | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure API Management (in regions where available):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Linux: Preview<br>Windows: Not available | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| User assigned | Linux: Preview<br>Windows: Not available | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure Container Instances (in regions where available):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| User assigned | Förhandsversion | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services that support Azure AD authentication

The following services support Azure AD authentication, and have been tested with client services that use managed identities for Azure resources.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Tillgänglig |
| Azure Government | `https://management.usgovcloudapi.net/` | Tillgänglig |
| Azure Germany | `https://management.microsoftazure.de/` | Tillgänglig |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | Tillgänglig |

### <a name="azure-key-vault"></a>Azure Key Vault

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Tillgänglig |
| Azure Government | `https://vault.usgovcloudapi.net` | Tillgänglig |
| Azure Germany |  `https://vault.microsoftazure.de` | Tillgänglig |
| Azure Kina 21Vianet | `https://vault.azure.cn` | Tillgänglig |

### <a name="azure-data-lake"></a>Azure Data Lake 

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Tillgänglig |
| Azure Government |  | Inte tillgänglig |
| Azure Germany |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-sql"></a>Azure SQL 

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Tillgänglig |
| Azure Government | `https://database.usgovcloudapi.net/` | Tillgänglig |
| Azure Germany | `https://database.cloudapi.de/` | Tillgänglig |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | Tillgänglig |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Tillgänglig |
| Azure Government |  | Inte tillgänglig |
| Azure Germany |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-service-bus"></a>Azure Service Bus

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Tillgänglig |
| Azure Government |  | Tillgänglig |
| Azure Germany |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Tillgänglig |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Tillgänglig |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Tillgänglig |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Tillgänglig |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| I molnet | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Tillgänglig |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Tillgänglig |
| Azure Germany | `https://*.asazure.cloudapi.de` | Tillgänglig |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | Tillgänglig |
