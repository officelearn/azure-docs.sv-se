---
title: Hanterade tjänstidentiteten (MSI) för Azure Active Directory
description: En översikt över hanterade tjänstidentiteten för Azure-resurser.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Hanterad Service identitet (MSI) för Azure-resurser

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

En gemensam utmaning när skapa molnprogram är hur du hanterar autentiseringsuppgifterna som måste vara i koden för att autentisera till molntjänster. Skydda autentiseringsuppgifterna är en viktig uppgift. Vi rekommenderar de aldrig visas på arbetsstationer eller checkas till källkontroll. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter och andra nycklar och hemligheter, men din kod måste autentisera till Key Vault de. Hanterad Service identitet (MSI) gör att lösa problemet enklare genom att ge en automatiskt hanterade identitet i Azure-tjänster i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan några autentiseringsuppgifter i koden.

## <a name="how-does-it-work"></a>Hur fungerar det?

När du använder en hanterad tjänstidentitet på en Azure-tjänst-instans, skapar Azure en identitet i Azure AD-klient som används av din Azure-prenumeration. Azure tillhandahåller dessutom autentiseringsuppgifterna för identitet, på instansen för tjänsten. Koden kan därför göra en lokal begäran om att få åtkomst-token för tjänster som stöder Azure AD-autentisering. Alla medan Azure tar hand om att de autentiseringsuppgifter som används av tjänstinstansen.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Hur aktiverar jag mina resurser du använder en hanterad tjänstidentitet?

Det finns två typer av hanterade Tjänsteidentiteter: *System tilldelade* och *användaren tilldelats*.

- En **System tilldelade** MSI aktiveras direkt på en instans av Azure-tjänsten. Hjälp att aktivera Azure skapar en identitet för tjänstinstansen i Azure AD-klient och etablerar autentiseringsuppgifterna för identiteten på tjänstinstansen. Livscykeln för ett system som är tilldelade MSI är direkt knuten till Azure tjänstinstans aktiveras på. Om tjänstinstansen tas bort rensas Azure automatiskt autentiseringsuppgifterna och identitet i Azure AD.

- En **användaren tilldelats** MSI *(privat förhandsvisning)* skapas som en fristående Azure-resurs. Hjälp att skapa skapar Azure en identitet i Azure AD-klient. När identiteten har skapats kan kan den tilldelas en eller flera instanser av Azure-tjänsten. Eftersom en Användartilldelad MSI kan användas av flera Azure-tjänstinstanser, hanteras separat dess livscykel.

Här är ett exempel på hur en automatiskt genererad MSI fungerar med Azure Virtual Machines.

![Virtual Machine MSI-exempel](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager får ett meddelande om att aktivera automatiskt tilldelade MSI på en virtuell dator.
2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD för att representera identiteten för den virtuella datorn. Tjänstens huvudnamn har skapats i Azure AD-klient som är betrodd av den här prenumerationen.
3. Azure Resource Manager konfigurerar information för tjänstens huvudnamn i MSI VM-tillägget för den virtuella datorn. Det här steget omfattar konfigurering av klient-ID och certifikat som används av tillägget för att få åtkomst-token från Azure AD.
4. Nu när tjänstens huvudnamn identiteten för den virtuella datorn är känt, kan det beviljas åtkomst till Azure-resurser. Exempelvis om din kod måste anropa Azure Resource Manager, vill du tilldela tjänstens huvudnamn för den virtuella datorn sedan rätt roll med hjälp av rollbaserad åtkomstkontroll (RBAC) i Azure AD.  Om din kod måste anropa Nyckelvalvet, skulle du ge din kodåtkomst till specifika secret eller nyckeln i Nyckelvalvet.
5. Din kod som körs på den virtuella datorn begär en token från en lokal slutpunkt som är värd MSI VM-tillägg: http://localhost:50342/oauth2/token. Resursparametern anger vilken tjänst som token som skickas. Till exempel om du vill att din kod för att autentisera till Azure Resource Manager du skulle använda resursen =https://management.azure.com/.
6. VM-tillägget MSI använder dess konfigurerade klient-ID och certifikat för att begära en åtkomst-token från Azure AD.  Azure AD returnerar en åtkomsttoken för JSON-Webbtoken (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

Med hjälp av samma diagrammets, här ett exempel på hur en Användartilldelad MSI fungerar med Azure Virtual Machines.

![Virtual Machine MSI-exempel](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager får ett meddelande om att skapa en Användartilldelad MSI.
2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD för att representera identiteten för MSI. Tjänstens huvudnamn har skapats i Azure AD-klient som är betrodd av den här prenumerationen.
3. Azure Resource Manager får ett meddelande om att konfigurera tjänstens huvudnamn information i MSI VM-tillägget för en virtuell dator. Det här steget omfattar konfigurering av klient-ID och certifikat som används av tillägget för att få åtkomst-token från Azure AD.
4. Nu när tjänstens huvudnamn identiteten för MSI-filerna är känd kan det beviljas åtkomst till Azure-resurser. Till exempel om din kod måste anropa Azure Resource Manager, vill du tilldela MSI-tjänstens huvudnamn sedan rätt roll med hjälp av rollbaserad åtkomstkontroll (RBAC) i Azure AD. Om din kod måste anropa Nyckelvalvet, skulle du ge din kodåtkomst till specifika secret eller nyckeln i Nyckelvalvet. Obs: Steg 3 krävs inte att slutföra steg 4. När en MSI finns kan det beviljas åtkomst till resurser, oavsett som konfigureras på en virtuell dator eller inte.
5. Din kod som körs på den virtuella datorn begär en token från en lokal slutpunkt som är värd MSI VM-tillägg: http://localhost:50342/oauth2/token. Klient-ID-parametern anger namnet på MSI-identitet om du ska använda. Dessutom anger resursparametern vilken tjänst som token som skickas. Till exempel om du vill att din kod för att autentisera till Azure Resource Manager du skulle använda resursen =https://management.azure.com/.
6. VM-tillägget MSI kontrollerar om certifikatet för det begärda klient-ID har konfigurerats och begär en åtkomst-token från Azure AD. Azure AD returnerar en åtkomsttoken för JSON-Webbtoken (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

Varje Azure-tjänst som stöder hanterade tjänstidentiteten har sin egen metod för din kod att hämta en åtkomst-token. Gå igenom självstudierna för varje tjänst och ta reda på den specifika metoden att hämta en token.

## <a name="try-managed-service-identity"></a>Försök hanterade tjänstidentiteten

Försök hanterade tjänstidentiteten självstudiekursen Läs slutpunkt till slutpunkt-scenarier för att komma åt olika Azure-resurser:
<br><br>
| Från MSI-aktiverade resurs | Lär dig att |
| ------- | -------- |
| Azure VM (Linux)   | [Åtkomst till Azure Resource Manager med en virtuell Linux-dator hanterade tjänstidentiteten](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Åtkomst till Azure Storage via åtkomstnyckel med hanterade tjänstidentiteten en Linux VM](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Hanterade tjänstidentiteten stöd för vilka Azure-tjänster?

Azure-tjänster som stöder hanterade tjänstidentiteten kan använda MSI för att autentisera tjänster som stöder Azure AD-autentisering.  Vi håller på att integrera MSI och Azure AD authentication i Azure.  Kontrollera tillbaka ofta för uppdateringar.

### <a name="azure-services-that-support-managed-service-identity"></a>Azure-tjänster som stöder hanterade tjänstidentiteten

Hanterade tjänstidentiteten stöd för följande Azure-tjänster.

| Tjänst | Status | Date | Konfigurera | Hämta en token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Förhandsversion | September 2017 | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Azure Resource Manager-mallar](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP-REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade tjänstidentiteten.

| Tjänst | Resurs-ID | Status | Date | Tilldela åtkomst |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Tillgänglig | September 2017 | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Tillgänglig | September 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Tillgänglig | September 2017 | |
| Azure SQL | https://database.windows.net/ | Tillgänglig | Oktober 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Hur mycket kostar hanterade tjänstidentiteten?

Hanterade tjänstidentiteten som levereras med Azure Active Directory ledigt, vilket är standard för Azure-prenumerationer.  Det finns utan extra kostnad för hanterade tjänstidentiteten.

## <a name="support-and-feedback"></a>Support och feedback

Vi vill gärna höra av dig!

* Ställ frågor på Stack Overflow med taggen [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Se funktion begäranden eller ge feedback på den [Azure AD Feedbackforum för utvecklare](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






