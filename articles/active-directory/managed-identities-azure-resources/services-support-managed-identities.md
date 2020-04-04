---
title: Azure Services som stöder hanterade identiteter - Azure AD
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c7a65df100cd58561ce12ac2ae01281eebd419a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656053"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till alla tjänster som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i koden. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster stöder hanterade identiteter för Azure-resurser:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | Förhandsversion | Förhandsversion | 
| Tilldelad användare | ![Tillgängliga][check] | ![Tillgängliga][check] | Förhandsversion | Förhandsversion |

Se följande lista för att konfigurera hanterad identitet för virtuella Azure-datorer (i regioner där sådana finns):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | Förhandsversion | Förhandsversion | Förhandsversion |
| Tilldelad användare | ![Tillgängliga][check] | Förhandsversion | Förhandsversion | Förhandsversion |

Se följande lista för att konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | ![Tillgängliga][check] | ![Tillgängliga][check] |
| Tilldelad användare | ![Tillgängliga][check] | ![Tillgängliga][check]  | ![Tillgängliga][check]  | ![Tillgängliga][check] |

Se följande lista för att konfigurera hanterad identitet för Azure App Service (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | Inte tillgängligt |

Se följande lista om du vill använda en hanterad identitet med [Azure Blueprints:](../../governance/blueprints/overview.md)

- [Azure portal - skisstilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - skisstilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Hanterad identitetstyp |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | ![Tillgängliga][check] | ![Tillgängliga][check] |
| Tilldelad användare | ![Tillgängliga][check] | ![Tillgängliga][check]  | ![Tillgängliga][check]  | ![Tillgängliga][check]  |

Se följande lista för att konfigurera hanterad identitet för Azure Functions (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | ![Tillgängliga][check] |
| Tilldelad användare | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | ![Tillgängliga][check] |


Se följande lista för att konfigurera hanterad identitet för Azure Logic Apps (i regioner där det är tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | ![Tillgängliga][check] |
| Tilldelad användare | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Data Factory V2 (i regioner där det är tillgängligt):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | ![Tillgängliga][check] | Inte tillgängligt | ![Tillgängliga][check] |
| Tilldelad användare | Förhandsversion | Förhandsversion | Inte tillgängligt | Förhandsversion |

Se följande lista för att konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | Linux: Förhandsvisning<br>Windows: Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | Linux: Förhandsvisning<br>Windows: Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Container Instances (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | Förhandsversion | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista för att konfigurera hanterad identitet för Azure Container Registry Tasks (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Hanterade identitet för serviceinfrastrukturprogram](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) finns i förhandsversion och är tillgängligt i alla regioner.

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgängliga][check] | Inte tillgänglig | Inte tillgänglig | inte tillgänglig |
| Tilldelad användare | ![Tillgängliga][check] | Inte tillgänglig | Inte tillgänglig |Inte tillgänglig |

I följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:
- [Azure Resource Manager-mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

I följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure-portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager-mall](../../role-based-access-control/role-assignments-template.md)

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Tillgängliga][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Tillgängliga][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Tillgängliga][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Tillgängliga][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Tillgängliga][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Tillgängliga][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Tillgängliga][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Tillgängliga][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Tillgängliga][check] |
| Azure Government |  | Inte tillgänglig |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-sql"></a>Azure SQL 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Tillgängliga][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Tillgängliga][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Tillgängliga][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Tillgängliga][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Tillgängliga][check] |
| Azure Government |  | Inte tillgänglig |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-service-bus"></a>Azure Service Bus

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Tillgängliga][check] |
| Azure Government |  | ![Tillgängliga][check] |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Tillgängliga][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Tillgängliga][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Tillgängliga][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Tillgängliga][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Tillgängliga][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Tillgängliga][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Tillgängliga][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Tillgängliga][check] |


[check]: media/services-support-managed-identities/check.png "Tillgängliga"
