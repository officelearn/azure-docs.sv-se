---
title: Azure-tjänster som har stöd för hanterade identiteter för Azure-resurser
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5630c157ef7843726bcc17455a1e683e58b88ffd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905243"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster har stöd för hanterade identiteter för Azure-resurser:

### <a name="azure-virtual-machines"></a>Azure virtuella maskiner

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Förhandsversion | Förhandsversion | Förhandsversion | 
| Användartilldelad | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure-Virtual Machine Scale Sets

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Förhandsversion | Förhandsversion | Förhandsversion |
| Användartilldelad | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| Användartilldelad | Tillgängligt | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure App Service (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Förhandsversion | Saknas | Saknas | Saknas |
| Användartilldelad | Förhandsversion | Saknas | Saknas | Saknas |

Se följande lista för att använda en hanterad identitet med [Azure](../../governance/blueprints/overview.md)-skisser:

- [Azure Portal skiss tilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API skiss tilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| Användartilldelad | Tillgängligt | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure Functions (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Förhandsversion | Förhandsversion | Saknas | Förhandsversion |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure Logic Apps (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Saknas | Saknas | Saknas |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Saknas | Saknas |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Linux: Förhandsversion<br>Windows: Saknas | Saknas | Saknas | Saknas |
| Användartilldelad | Linux: Förhandsversion<br>Windows: Saknas | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure Container Instances (i regioner där det finns tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Saknas | Saknas | Saknas |
| Användartilldelad | Förhandsversion | Saknas | Saknas | Saknas |

I följande lista kan du konfigurera hanterad identitet för Azure Container Registry uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klient tjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure Portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager mall](../../role-based-access-control/role-assignments-template.md)

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Tillgängligt |
| Azure Government | `https://management.usgovcloudapi.net/` | Tillgängligt |
| Azure Tyskland | `https://management.microsoftazure.de/` | Tillgängligt |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | Tillgängligt |

### <a name="azure-key-vault"></a>Azure Key Vault

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Tillgängligt |
| Azure Government | `https://vault.usgovcloudapi.net` | Tillgängligt |
| Azure Tyskland |  `https://vault.microsoftazure.de` | Tillgängligt |
| Azure Kina 21Vianet | `https://vault.azure.cn` | Tillgängligt |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Tillgängligt |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-sql"></a>Azure SQL 

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Tillgängligt |
| Azure Government | `https://database.usgovcloudapi.net/` | Tillgängligt |
| Azure Tyskland | `https://database.cloudapi.de/` | Tillgängligt |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | Tillgängligt |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Förhandsversion |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-service-bus"></a>Azure Service Bus

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Förhandsversion |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Tillgängligt |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Tillgängligt |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Tillgängligt |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Tillgängligt |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Molnet | Resurs-ID | State |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Tillgängligt |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Tillgängligt |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | Tillgängligt |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | Tillgängligt |
