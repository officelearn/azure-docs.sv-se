---
title: Azure-tjänster som har stöd för hanterad tjänstidentitet
description: Lista över tjänster som har stöd för hanterad tjänstidentitet och Azure AD-autentisering
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 04247792f8467ecf51a074d1e03bbd6e7c553a02
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247263"
---
# <a name="services-that-support-managed-service-identity"></a>Tjänster som stöder hanterad tjänstidentitet 

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet, kan du autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. Vi håller på att integrera hanterad tjänstidentitet och Azure AD-autentisering i Azure. Kontrollera tillbaka ofta för uppdateringar.

## <a name="azure-services-that-support-managed-service-identity"></a>Azure-tjänster som har stöd för hanterad tjänstidentitet

Hanterad tjänstidentitet stöd för följande Azure-tjänster.

| Tjänst | Status för systemtilldelad | Status för användartilldelade| Konfigurera | Hämta en token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Förhandsversion | Förhandsversion | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | Förhandsversion | Förhandsversion | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure Resource Manager-mallar](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Tillgängligt | Inte tillgängligt | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Tillgängligt | Inte tillgängligt | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Förhandsversion | Inte tillgängligt | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | Tillgängligt | Inte tillgängligt | [Azure Resource Manager-mall](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade tjänstidentiteten.

| Tjänst | Resurs-ID | Status | Date | Tilldela åtkomst |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Tillgängligt | September 2017 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Tillgängligt | September 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Tillgängligt | September 2017 | |
| Azure SQL | https://database.windows.net/ | Tillgängligt | Oktober 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | Tillgängligt | December 2017 | |
| Azure Service Bus | https://servicebus.azure.net | Tillgängligt | December 2017 | |
| Azure Storage | https://azure.microsoft.com/services/storage/ | Förhandsversion | Maj 2018 | |
