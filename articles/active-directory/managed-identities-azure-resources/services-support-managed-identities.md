---
title: Azure-tjänster som har stöd för hanterade identiteter – Azure AD
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 03/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3493734ea7d0ed7b0703a7adef0d1051e64fc6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366009"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster har stöd för hanterade identiteter för Azure-resurser:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | Förhandsversion | Förhandsversion | Förhandsversion | 
| Tilldelad användare | ![Få][check] | Förhandsversion | Förhandsversion | Förhandsversion |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure-Virtual Machine Scale Sets

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | Förhandsversion | Förhandsversion | Förhandsversion |
| Tilldelad användare | ![Få][check] | Förhandsversion | Förhandsversion | Förhandsversion |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Tilldelad användare | ![Få][check] | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure App Service (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | ![Få][check] | ![Få][check] | Inte tillgängligt | Inte tillgängligt |

Se följande lista för att använda en hanterad identitet med [Azure-skisser](../../governance/blueprints/overview.md):

- [Azure Portal skiss tilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API skiss tilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Tilldelad användare | ![Få][check] | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure Functions (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | Inte tillgängligt | ![Få][check] |
| Tilldelad användare | ![Få][check] | ![Få][check] | Inte tillgängligt | ![Få][check] |


I följande lista kan du konfigurera hanterad identitet för Azure Logic Apps (i regioner där det finns tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | Inte tillgängligt | ![Få][check] |
| Tilldelad användare | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | ![Få][check] | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure Container Instances (i regioner där det finns tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Tilldelad användare | Förhandsversion | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

I följande lista kan du konfigurera hanterad identitet för Azure Container Registry uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Hanterad identitet för Service Fabric program](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) är i för hands version och är tillgänglig i alla regioner.

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Tilldelat system | ![Få][check] | Inte tillgänglig | Inte tillgänglig | inte tillgängligt |
| Tilldelad användare | ![Få][check] | Inte tillgänglig | Inte tillgänglig |Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:
- [Azure Resource Manager-mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

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
| Azure Global | `https://management.azure.com/`| ![Få][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Få][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Få][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Få][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Få][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Få][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Få][check] |
| Azure Government |  | Inte tillgänglig |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-sql"></a>Azure SQL 

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Få][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Få][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Få][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Få][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Få][check] |
| Azure Government |  | Inte tillgänglig |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |

### <a name="azure-service-bus"></a>Azure Service Bus

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Få][check] |
| Azure Government |  | ![Få][check] |
| Azure Tyskland |   | Inte tillgänglig |
| Azure Kina 21Vianet |  | Inte tillgänglig |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Få][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Få][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Molnet | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Få][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Få][check] |


[check]: media/services-support-managed-identities/check.png "Få"
