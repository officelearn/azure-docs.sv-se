---
title: Kunden fakturerings- och återbetalning i Azure-stacken | Microsoft Docs
description: Lär dig hur du hämtar information om användning från Azure-stacken.
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
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877491"
---
# <a name="usage-and-billing-in-azure-stack"></a>Användnings- och fakturering i Azure-stacken

Den här artikeln beskriver hur användare i Azure-stacken debiteras för Resursanvändning. Du kan lära dig hur faktureringsinformation är tillgängligt för analys och kostnad tillbaka.

Azure-stacken samlar in grupper användningsdata för alla resurser som används och vidarebefordrar dessa data till Azure handel. Azure Commerce debiterar dig för Azure Stack-användning på samma sätt som den skulle debitera dig för Azure-användning.

Du kan också hämta användningsdata och exportera den till egna fakturerings- eller kostnad säkerhetskopieringssystem med hjälp av ett fakturerings-kort eller exportera den till en business intelligence-verktyg som till exempel Microsoft Power BI och använda för analys.


## <a name="usage-pipeline"></a>Användning av pipeline

Varje resursprovider i Azure-stacken avger användningsdata per Resursanvändning. Webbtjänsten Usage regelbundet (varje timme och dag) sammanställer användningsdata och lagrar den i användningsdatabasen. Azure Stack operatörer och användare kan komma åt lagrade användningsdata via API: er för Azure-stacken resurs användning. 

Om du har [registrerad Azure Stack-instans med Azure](azure-stack-register.md), Azure-stacken är konfigurerad för att skicka användningsdata till Azure handel. När data överförs till Azure kan du komma åt den fakturerings-portalen eller med hjälp av Azure Resource användning API: er. Referera till den [data användningsrapport](azure-stack-usage-reporting.md) Läs mer om vilka rapporteras mer data till Azure.  

Följande bild visar viktiga komponenter i pipeline för användning: 

![Användning av pipeline](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Information om vilka användning kan hitta, och hur?

Azure-stacken resurs-leverantörer, till exempel bearbetning, lagring och nätverk, generera användningsdata i timmen för varje prenumeration. Användningsdata innehåller information om resursen används som resursnamn, prenumeration som används och kvantitet som används. Mer information om mätare ID resurser, referera till den [användning API FAQ](azure-stack-usage-related-faq.md) artikel.

När användningsdata har samlats in, är det [rapporteras till Azure](azure-stack-usage-reporting.md) att generera en faktura som kan granskas via Azure portal för fakturering. 


> [!NOTE]
> Rapportering av användningsdata krävs inte för Azure-stacken Development Kit och Azure-stacken integrerat systemanvändare som har licens under kapacitet modellen. Läs mer om licensiering i Azure-stacken i den [paketering och prissättning](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) datablad.

Azure-fakturering portalen visar användningsdata för de avgiftsbelagda resurserna. Utöver resurserna som avgiftsbelagda Azure Stack samlar in användningsdata för en bredare uppsättning resurser som du kan komma åt i Azure-stacken miljön via REST API: er eller PowerShell. Azure Stack-operatörer kan hämta användningsdata för alla användare prenumerationer. Enskilda användare kan bara hämta deras användningsinformation. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Rapport för multitenant molntjänst-Providers

Multitenant molntjänst-providers (CSP) som har många kunder som använder Azure-stacken kanske vill rapportera användning av varje kund separat, så att leverantören kan debitera användning till olika Azure-prenumerationer. 

Varje kund ska ha sin identitet som representeras av en annan klient i Azure Active Directory (AD Azure). Tilldela en CSP-prenumeration till varje Azure AD-klient har stöd för Azure-stacken. Du kan lägga till klienter och deras prenumerationer grundläggande Azure Stack-registreringen. Grundläggande registreringen är klar för alla Azure-stackar. Om en prenumeration har inte registrerats för en klient, kan användaren fortfarande använda Azure-stacken och deras användning ska skickas till prenumerationen används för registreringen av grundläggande. 


## <a name="next-steps"></a>Nästa steg

[Registrera med Azure-stacken](azure-stack-registration.md)

[Rapportera Azure Stack användningsdata till Azure](azure-stack-usage-reporting.md)

[Providern Resursanvändning API](azure-stack-provider-resource-api.md)

[Resursanvändning API-klient](azure-stack-tenant-resource-usage-api.md)

[Användning-relaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)