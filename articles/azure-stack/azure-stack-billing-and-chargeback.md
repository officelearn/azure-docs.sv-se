---
title: Kundfaktureringen och förbrukning i Azure Stack | Microsoft Docs
description: Lär dig hur du hämtar information om användning från Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337858"
---
# <a name="usage-and-billing-in-azure-stack"></a>Användning och fakturering i Azure Stack

Den här artikeln beskriver hur Azure Stack-användare debiteras för användning och hur faktureringsinformationen är tillgängligt för analys och kostnad tillbaka.

Azure Stack samlar in och grupperar användningsdata för resurser som används. Azure Stack vidarebefordrar sedan dessa data till Azure Commerce. Azure Commerce debiteras du för Azure Stack-användning på samma sätt som det debiteras du för Azure-användning.

Du kan också få användningsdata och exportera den till din egen fakturerings- eller säkerhetskopieringssystem genom att använda en fakturering kortet, eller exportera den till en business intelligence-verktyg som Microsoft Power BI.

## <a name="usage-pipeline"></a>Användning-pipeline

Varje resursprovider i Azure Stack inlägg användningsdata per användning. Webbtjänsten usage regelbundet (varje timme och dag) sammanställer användningsdata och lagrar den i användningsdatabasen. Azure Stack-operatörer och användare kan komma åt de lagrade användningsdata via Azure Stack-Resursanvändning API: er.

Om du har [registrerat din instans av Azure Stack med Azure](azure-stack-register.md), Azure Stack är konfigurerad för att skicka användningsdata till Azure Commerce. När data har överförts till Azure kan du komma åt det via på faktureringsportal eller med hjälp av Azure-Resursanvändning API: er. Mer information om vilka användningsdata rapporteras till Azure finns i [data användningsrapportering](azure-stack-usage-reporting.md).  

Följande bild visar de viktigaste komponenterna i pipelinen användning:

![Användning-pipeline](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Vilka användningsinformation kan hitta, och hur?

Azure Stack-resursproviders (till exempel beräkning, lagring och nätverk) generera användningsdata i timmen för varje prenumeration. Användningsdata innehåller information om resursen används, till exempel resursens namn, prenumeration som används och kvantitet som används. Läs om de mätare ID resurser i den [användning API vanliga frågor och svar](azure-stack-usage-related-faq.md).

När användningsdata har samlats in, är det [rapporteras till Azure](azure-stack-usage-reporting.md) att generera en faktura som kan ses via på Azures faktureringsportal.

> [!NOTE]  
> Rapportering av användning krävs inte för Azure Stack Development Kit (ASDK) och för integrerade Azure Stack systemanvändare med licens under kapacitet modellen. Läs mer om licensiering i Azure Stack i den [paketering och priser datablad](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

På Azures faktureringsportal visar användningsdata för resurserna som debiterbar. Förutom de avgiftsbelagda resurserna, Azure Stack samlar in användningsdata för en bredare uppsättning resurser som du kan komma åt i Azure Stack-miljön via REST API: er eller PowerShell-cmdletar. Azure Stack-operatörer kan hämta användningsdata för alla prenumerationer för användare. Enskilda användare kan bara hämta sina egna användningsinformation.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Rapport för multitenant Molntjänstleverantörer

En flera innehavare Cloud Service Provider (CSP) som har många kunder som använder Azure Stack kanske vill rapportera varje KUNDANVÄNDNING separat, så att providern kan debitera för användningen av olika Azure-prenumerationer.

Varje kund har sin identitet som representeras av en annan Azure Active Directory (Azure AD)-klient. Tilldela en CSP-prenumeration till varje Azure AD-klienten har stöd för Azure Stack. Du kan lägga till klienter och deras prenumerationer grundläggande Azure Stack-registreringen. Grundläggande registreringen är klar för alla Azure Stack-instanser. Om en prenumeration inte har registrerats för en klient, användaren kan fortfarande använda Azure Stack och deras användning kommer att skickas till den prenumeration som används för grundläggande registreringen.

## <a name="next-steps"></a>Nästa steg

- [Registrera med Azure Stack](azure-stack-registration.md)
- [Rapportera användningsdata för Azure Stack till Azure](azure-stack-usage-reporting.md)
- [Providerresursanvändning](azure-stack-provider-resource-api.md)
- [API för klientresursanvändning](azure-stack-tenant-resource-usage-api.md)
- [Användningsrelaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)