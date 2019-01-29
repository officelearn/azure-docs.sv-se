---
title: Azure-tjänster som stöder hanterade identiteter för Azure-resurser
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.openlocfilehash: 2ca70805a0806078613bb37ac1d2836dd8d626c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180746"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet, kan du autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. Vi håller integrerande hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kontrollera tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster stöder hanterade identiteter för Azure-resurser:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

|Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion |
| Användartilldelad | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion

Se listan nedan för att konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Förhandsversion | Förhandsversion | Förhandsversion |
| Användartilldelad | Förhandsversion | Förhandsversion | Förhandsversion | Förhandsversion

Se listan nedan för att konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| Användartilldelad | Förhandsversion | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure App Service (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| Användartilldelad | Förhandsversion | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure Functions (i regioner där det är tillgängligt):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure Logic Apps (i regioner där det är tillgängligt):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-mall](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure Data Factory V2 (i regioner där det är tillgängligt):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt | Tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Hanterade identitetstypen |  Allmänt tillgängligt<br>Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| Systemtilldelad | Linux: Förhandsversion<br>Windows: Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Användartilldelad | Linux: Förhandsversion<br>Windows: Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt

Se listan nedan för att konfigurera hanterad identitet för Azure Container Instances (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade identiteter för Azure-resurser.

| Tjänst | Resurs-ID | Status | Tilldela åtkomst |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Tillgängligt | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager-mall](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | Tillgängligt |  
| Azure Data Lake | `https://datalake.azure.net/` | Tillgängligt |
| Azure SQL | `https://database.windows.net/` | Tillgängligt |
| Azure Event Hubs | `https://eventhubs.azure.net` | Förhandsversion |
| Azure Service Bus | `https://servicebus.azure.net` | Förhandsversion |
| Azure Storage | `https://storage.azure.com/` | Förhandsversion |
