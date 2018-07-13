---
title: Kunden fakturerings- och Chargeback i Azure Stack | Microsoft Docs
description: Lär dig hur du hämtar information om användning från Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f2449176f96b18a374ff6d54fbf7e09c8f5e21cc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003585"
---
# <a name="usage-and-billing-in-azure-stack"></a>Användning och fakturering i Azure Stack

Den här artikeln beskriver hur Azure Stack-användare debiteras för användning. Du kan lära dig hur faktureringsinformation är tillgängligt för analys och kostnad tillbaka.

Azure Stack samlar in och grupperar användningsdata för använda resurser. Azure Stack vidarebefordrar sedan dessa data till Azure Commerce. Azure Commerce debiteras du för Azure Stack-användning på samma sätt som den skulle faktureras du för Azure-användning.

Du kan också få användningsdata och exportera den till din egen fakturerings- eller säkerhetskopieringssystem genom att använda en fakturering kortet, eller exportera den till en business intelligence-verktyg som Microsoft Power BI.


## <a name="usage-pipeline"></a>Användning-pipeline

Varje resursprovider i Azure Stack inlägg användningsdata per användning. Webbtjänsten Usage regelbundet (varje timme och dag) sammanställer användningsdata och lagrar den i användningsdatabasen. Azure Stack-operatörer och användare kan komma åt de lagrade användningsdata via API: er för Azure Stack Resource användning. 

Om du har [registrerat din instans av Azure Stack med Azure](azure-stack-register.md), Azure Stack är konfigurerad för att skicka användningsdata till Azure Commerce. När data har överförts till Azure kan du använda tjänsten via faktureringsportalen eller med hjälp av API: erna för användning av Azure Resource. Referera till den [data användningsrapportering](azure-stack-usage-reporting.md) du lär dig mer om vilka användning även data rapporteras till Azure.  

Följande bild visar de viktigaste komponenterna i pipelinen användning: 

![Användning-pipeline](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Vilka användningsinformation kan hitta, och hur?

Azure Stack-resursproviders (till exempel beräkning, lagring och nätverk) generera användningsdata i timmen för varje prenumeration. Användningsdata innehåller information om resursen används som resursens namn, prenumeration som används och kvantitet som används. Mer information om resurser för mätare-ID, referera till den [användning API vanliga frågor och svar](azure-stack-usage-related-faq.md) artikeln.

När användningsdata har samlats in, är det [rapporteras till Azure](azure-stack-usage-reporting.md) att generera en faktura som kan ses via på Azures faktureringsportal. 

> [!NOTE]  
> Rapportering av användning krävs inte för Azure Stack Development Kit och integrerade Azure Stack systemanvändare med licens under kapacitet modellen. Läs mer om licensiering i Azure Stack i den [paketering och priser](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) datablad.

På Azures faktureringsportal visar användningsdata för resurserna som debiterbar. Förutom de avgiftsbelagda resurserna, Azure Stack samlar in användningsdata för en bredare uppsättning resurser som du kan komma åt i Azure Stack-miljön via REST API: er eller PowerShell. Azure Stack-operatörer kan hämta användningsdata för alla prenumerationer för användare. Enskilda användare kan bara hämta deras användningsinformation. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Rapport för multitenant Molntjänstleverantörer

Multitenant Cloud Service Provider (CSP) som har många kunder som använder Azure Stack kanske vill rapportera varje KUNDANVÄNDNING separat, så att providern kan debitera för användningen av olika Azure-prenumerationer. 

Varje kund ska ha sin identitet som representeras av en annan Azure Active Directory (Azure AD)-klient. Tilldela en CSP-prenumeration till varje Azure AD-klienten har stöd för Azure Stack. Du kan lägga till klienter och deras prenumerationer grundläggande Azure Stack-registreringen. Grundläggande registreringen är klar för alla Azure-stackar. Om en prenumeration inte har registrerats för en klient kan användaren fortfarande kan använda Azure Stack och deras användning kommer att skickas till den prenumeration som används för grundläggande registreringen. 


## <a name="next-steps"></a>Nästa steg

[Registrera med Azure Stack](azure-stack-registration.md)

[Rapportera användningsdata för Azure Stack till Azure](azure-stack-usage-reporting.md)

[Providerresursanvändning](azure-stack-provider-resource-api.md)

[Klientresursanvändning](azure-stack-tenant-resource-usage-api.md)

[Användningsrelaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)