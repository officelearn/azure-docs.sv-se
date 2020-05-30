---
title: Azure-tjänster som har stöd för hanterade identiteter – Azure AD
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 05/12/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 62d1f8d0e6fb5b6b6afb28f5db4ae8b818cc4705
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194858"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster har stöd för hanterade identiteter för Azure-resurser:


### <a name="azure-api-management"></a>Azure API Management

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | ![Tillgänglig][check] |
| Tilldelad användare | Förhandsgranskning | Förhandsgranskning | Inte tillgänglig | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager mall](/azure/api-management/api-management-howto-use-managed-service-identity)


### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] |
| Tilldelad användare | ![Tillgänglig][check] | ![Tillgänglig][check]  | ![Tillgänglig][check]  | ![Tillgänglig][check] |

I följande lista kan du konfigurera hanterad identitet för Azure App Service (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)


### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Tilldelad användare | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att använda en hanterad identitet med [Azure-skisser](../../governance/blueprints/overview.md):

- [Azure Portal skiss tilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API skiss tilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Tilldelad användare | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Instances (i regioner där det finns tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Tilldelad användare | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Registry uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)


### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | ![Tillgänglig][check] |
| Tilldelad användare | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)


### <a name="azure-functions"></a>Azure Functions

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] |
| Tilldelad användare | ![Tillgänglig][check] | ![Tillgänglig][check]  | ![Tillgänglig][check]  | ![Tillgänglig][check]  |

I följande lista kan du konfigurera hanterad identitet för Azure Functions (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | ![Tillgänglig][check] |
| Tilldelad användare | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | - | - | - | 
| Tilldelad användare | ![Tillgänglig][check] | - | - | - |


Mer information finns i [använda hanterade identiteter i Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/use-managed-identity).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | ![Tillgänglig][check] |
| Tilldelad användare | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | ![Tillgänglig][check] |


I följande lista kan du konfigurera hanterad identitet för Azure Logic Apps (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager mall](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)


### <a name="azure-service-fabric"></a>Azure Service Fabric
[Hanterad identitet för Service Fabric program](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) är i för hands version och är tillgänglig i alla regioner.

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | Inte tillgängligt | Inte tillgängligt | inte tillgängligt |
| Tilldelad användare | ![Tillgänglig][check] | Inte tillgängligt | Inte tillgängligt |Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:
- [Azure Resource Manager mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)



### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | Förhandsgranskning |
| Tilldelad användare | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Tillgänglig][check] | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | 
| Tilldelad användare | ![Tillgänglig][check] | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)




## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klient tjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure Portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager mall](../../role-based-access-control/role-assignments-template.md)

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Tillgänglig][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Tillgänglig][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Tillgänglig][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Tillgänglig][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Tillgänglig][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Tillgänglig][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-sql"></a>Azure SQL 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Tillgänglig][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Tillgänglig][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Tillgänglig][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Tillgänglig][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-service-bus"></a>Azure Service Bus

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Tillgänglig][check] |
| Azure Government |  | ![Tillgänglig][check] |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Tillgänglig][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Tillgänglig][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Tillgänglig][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Tillgänglig][check] |

> [!Note]
> Microsoft Power BI [stöder också hanterade identiteter](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).


[check]: media/services-support-managed-identities/check.png "Få"
