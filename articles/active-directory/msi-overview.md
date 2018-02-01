---
title: "Hanterade tjänstidentiteten (MSI) för Azure Active Directory"
description: "En översikt över hanterade tjänstidentiteten för Azure-resurser."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: b28f5c7c90783c5abd933b50ddb9631739b8f421
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Hanterad Service identitet (MSI) för Azure-resurser

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

En gemensam utmaning när skapa molnprogram är hur du hanterar autentiseringsuppgifterna som måste vara i koden för att autentisera till molntjänster. Skydda autentiseringsuppgifterna är en viktig uppgift. Vi rekommenderar de aldrig visas på arbetsstationer eller checkas till källkontroll. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter och andra nycklar och hemligheter, men din kod måste autentisera till Key Vault de. Hanterad Service identitet (MSI) gör att lösa problemet enklare genom att ge en automatiskt hanterade identitet i Azure-tjänster i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan några autentiseringsuppgifter i koden.

## <a name="how-does-it-work"></a>Hur fungerar det?

När du aktiverar hanterade tjänstidentiteten på en Azure-tjänst skapas en identitet för tjänstinstansen automatiskt i Azure AD-klient som används av din Azure-prenumeration i Azure.  Under försättsbladen etablerar Azure autentiseringsuppgifterna för identiteten på tjänstinstansen.  Din kod kan göra en lokal begäran om att få åtkomst-token för tjänster som stöder Azure AD-autentisering.  Azure hand tar om att de autentiseringsuppgifter som används av tjänstinstansen.  Om tjänstinstansen tas bort rensas Azure automatiskt autentiseringsuppgifterna och identitet i Azure AD.

Här är ett exempel på hur hanterade tjänstidentiteten fungerar med Azure Virtual Machines.

![Virtual Machine MSI-exempel](./media/msi-vm-example.png)

1. Azure Resource Manager får ett meddelande om att aktivera MSI på en virtuell dator.
2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD för att representera identiteten för den virtuella datorn. Tjänstens huvudnamn har skapats i Azure AD-klient som är betrodd av den här prenumerationen.
3. Azure Resource Manager konfigurerar information för tjänstens huvudnamn i MSI VM-tillägget för den virtuella datorn.  Det här steget omfattar konfigurering av klient-ID och certifikat som används av tillägget för att få åtkomst-token från Azure AD.
4. Nu när tjänstens huvudnamn identiteten för den virtuella datorn är känt, kan det beviljas åtkomst till Azure-resurser.  Exempelvis om din kod måste anropa Azure Resource Manager, vill du tilldela tjänstens huvudnamn för den virtuella datorn sedan rätt roll med hjälp av rollbaserad åtkomstkontroll (RBAC) i Azure AD.  Om din kod måste anropa Nyckelvalvet, skulle du ge din kodåtkomst till specifika secret eller nyckeln i Nyckelvalvet.
5. Din kod som körs på den virtuella datorn begär en token från en lokal slutpunkt som är värd MSI VM-tillägg: http://localhost:50342-oauth2-token.  Resursparametern anger vilken tjänst som token som skickas. Till exempel om du vill att din kod för att autentisera till Azure Resource Manager du skulle använda resursen = https://management.azure.com/.
6. VM-tillägget MSI använder dess konfigurerade klient-ID och certifikat för att begära en åtkomst-token från Azure AD.  Azure AD returnerar en åtkomsttoken för JSON-Webbtoken (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

Varje Azure-tjänst som stöder hanterade tjänstidentiteten har sin egen metod för din kod att hämta en åtkomst-token. Gå igenom självstudierna för varje tjänst och ta reda på den specifika metoden att hämta en token.

## <a name="try-managed-service-identity"></a>Försök hanterade tjänstidentiteten

Försök hanterade tjänstidentiteten självstudiekursen Läs slutpunkt till slutpunkt-scenarier för att komma åt olika Azure-resurser:
<br><br>
| Från MSI-aktiverade resurs | Lär dig att |
| ------- | -------- |
| Azure VM (Windows) | [Åtkomst till Azure Data Lake Store med Windows VM hanterade tjänstidentiteten](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Åtkomst till Azure Resource Manager med en Windows VM hanterade tjänstidentiteten](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Åtkomst till Azure SQL med Windows VM hanterade tjänstidentiteten](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Åtkomst till Azure Storage via åtkomstnyckel med en Windows VM hanteras tjänstidentitet](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Åtkomst till Azure Storage via SAS med en Windows VM hanterade tjänstidentiteten](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Åtkomst till en Azure-AD resurs med en Windows VM hanterade tjänstidentiteten och Azure Key Vault](msi-tutorial-windows-vm-access-nonaad.md) |
| Azure VM (Linux)   | [Åtkomst till Azure Data Lake Store med en virtuell Linux-dator hanterade tjänstidentiteten](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Åtkomst till Azure Resource Manager med en virtuell Linux-dator hanterade tjänstidentiteten](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Åtkomst till Azure Storage via åtkomstnyckel med hanterade tjänstidentiteten en Linux VM](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Åtkomst till Azure Storage via SAS med en virtuell Linux-dator hanterade tjänstidentiteten](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Åtkomst till en Azure-AD resurs med en hanterade tjänstidentiteten i Linux VM och Azure Key Vault](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Använda hanterade tjänstidentiteten med Azure App Service eller Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Function     | [Använda hanterade tjänstidentiteten med Azure App Service eller Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Använda hanterade tjänstidentiteten med Azure Service Bus](../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Använda hanterade tjänstidentiteten med Azure Event Hubs](../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Hanterade tjänstidentiteten stöd för vilka Azure-tjänster?

Azure-tjänster som stöder hanterade tjänstidentiteten kan använda MSI för att autentisera tjänster som stöder Azure AD-autentisering.  Vi håller på att integrera MSI och Azure AD authentication i Azure.  Kontrollera tillbaka ofta för uppdateringar.

### <a name="azure-services-that-support-managed-service-identity"></a>Azure-tjänster som stöder hanterade tjänstidentiteten

Hanterade tjänstidentiteten stöd för följande Azure-tjänster.

| Tjänst | Status | Date | Konfigurera | Hämta en token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Förhandsversion | September 2017 | [Azure-portalen](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Azure Resource Manager-mallar](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Gå](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Förhandsversion | September 2017 | [Azure-portalen](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Förhandsversion | September 2017 | [Azure-portalen](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-mall](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Förhandsversion | November 2017 | [Azure-portalen](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade tjänstidentiteten.

| Tjänst | Resurs-ID | Status | Date | Tilldela åtkomst |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Tillgänglig | September 2017 | [Azure-portalen](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Tillgänglig | September 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Tillgänglig | September 2017 | |
| Azure SQL | https://database.windows.net/ | Tillgänglig | Oktober 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net/ | Tillgänglig | December 2017 | |
| Azure Service Bus | https://servicebus.azure.net/ | Tillgänglig | December 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Hur mycket kostar hanterade tjänstidentiteten?

Hanterade tjänstidentiteten som levereras med Azure Active Directory ledigt, vilket är standard för Azure-prenumerationer.  Det finns utan extra kostnad för hanterade tjänstidentiteten.

## <a name="support-and-feedback"></a>Support och feedback

Vi vill gärna höra av dig!

* Ställ frågor på Stack Overflow med taggen [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Se funktion begäranden eller ge feedback på den [Azure AD Feedbackforum för utvecklare](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






