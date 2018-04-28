---
title: Azure-tjänster som stöder hanterade tjänstidentiteten
description: Lista över tjänster som stöder hanterade tjänstidentiteten och Azure AD-autentisering
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
manager: mtillman
ms.openlocfilehash: c78ba7b6c48ffddd44ef35340e9eb69a3ed32f40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="services-that-support-managed-service-identity"></a>Tjänster som stöder hanterade tjänstidentiteten 

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Med hjälp av en hanterad identitet, kan du autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. Vi håller på att integrera MSI och Azure AD authentication i Azure. Kontrollera tillbaka ofta för uppdateringar.

## <a name="azure-services-that-support-managed-service-identity"></a>Azure-tjänster som stöder hanterade tjänstidentiteten

Hanterade tjänstidentiteten stöd för följande Azure-tjänster.

| Tjänst | Status | Date | Konfigurera | Hämta en token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Förhandsversion | September 2017 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure Resource Manager-mallar](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Förhandsversion | September 2017 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Förhandsversion | September 2017 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Förhandsversion | November 2017 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade tjänstidentiteten.

| Tjänst | Resurs-ID | Status | Date | Tilldela åtkomst |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Tillgänglig | September 2017 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Tillgänglig | September 2017 | |
| Azure Data Lake | https://datalake.azure.net | Tillgänglig | September 2017 | |
| Azure SQL | https://database.windows.net | Tillgänglig | Oktober 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | Tillgänglig | December 2017 | |
| Azure Service Bus | https://servicebus.azure.net | Tillgänglig | December 2017 | |