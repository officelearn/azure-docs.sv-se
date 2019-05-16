---
title: Azure-tjänster som stöder hanterade identiteter för Azure-resurser
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 05/09/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0809423472bde0240145d43f4c28d9594b517928
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518694"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet, kan du autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. Vi håller integrerande hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kontrollera tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster stöder hanterade identiteter för Azure-resurser:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Förhandsgranska | Förhandsgranska | Förhandsgranska | 
| Användartilldelad | Förhandsgranska | Förhandsgranska | Förhandsgranska | Förhandsgranska |

Se listan nedan för att konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Förhandsgranska | Förhandsgranska | Förhandsgranska |
| Användartilldelad | Förhandsgranska | Förhandsgranska | Förhandsgranska | Förhandsgranska |

Se listan nedan för att konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| Användartilldelad | Förhandsgranska | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure App Service (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Förhandsgranska | Saknas | Saknas | Saknas |
| Användartilldelad | Förhandsgranska | Saknas | Saknas | Saknas |

Se listan nedan för att använda en hanterad identitet med [Azure skisser](../../governance/blueprints/overview.md):

- [Azure portal – skisstilldelningen](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST-API – skisstilldelningen](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Hanterade identitetstypen |Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| Användartilldelad | Förhandsgranska | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure Functions (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Förhandsgranska | Förhandsgranska | Saknas | Förhandsgranska |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure Logic Apps (i regioner där det är tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Saknas | Saknas | Saknas |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure Data Factory V2 (i regioner där det är tillgängligt):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgänglig | Tillgänglig | Saknas | Saknas |
| Användartilldelad | Saknas | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Hanterade identitetstypen | Allmänt tillgängligt<br>Global Azure Regions | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Linux: Förhandsgranska<br>Windows: Saknas | Saknas | Saknas | Saknas |
| Användartilldelad | Linux: Förhandsgranska<br>Windows: Saknas | Saknas | Saknas | Saknas |

Se listan nedan för att konfigurera hanterad identitet för Azure Container Instances (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se listan nedan för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure-portalen](howto-assign-access-portal.md)
- [Tilldela åtkomst via Powershell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager-mall](../../role-based-access-control/role-assignments-template.md)

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Tillgänglig |
| Azure Government | `https://management.usgovcloudapi.net/` | Tillgänglig |
| Azure Tyskland | `https://management.microsoftazure.de/` | Tillgänglig |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | Tillgänglig |

### <a name="azure-key-vault"></a>Azure Key Vault

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Tillgänglig |
| Azure Government | `https://vault.usgovcloudapi.net` | Tillgänglig |
| Azure Tyskland |  `https://vault.microsoftazure.de` | Tillgänglig |
| Azure Kina 21Vianet | `https://vault.azure.cn` | Tillgänglig |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Tillgänglig |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-sql"></a>Azure SQL 

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Tillgänglig |
| Azure Government | `https://database.usgovcloudapi.net/` | Tillgänglig |
| Azure Tyskland | `https://database.cloudapi.de/` | Tillgänglig |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | Tillgänglig |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Förhandsgranska |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-service-bus"></a>Azure Service Bus

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Förhandsgranska |
| Azure Government |  | Ej tillgänglig |
| Azure Tyskland |   | Ej tillgänglig |
| Azure Kina 21Vianet |  | Ej tillgänglig |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobbar och köer

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | Tillgänglig |
| Azure Government | `https://storage.azure.com/` | Tillgänglig |
| Azure Tyskland | `https://storage.azure.com/` | Tillgänglig |
| Azure Kina 21Vianet | `https://storage.azure.com/` | Tillgänglig |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Moln | Resurs-ID | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Tillgänglig |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Tillgänglig |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | Tillgänglig |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | Tillgänglig |