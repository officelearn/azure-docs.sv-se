---
title: Planer och fakturering
description: Läs mer om planer och fakturering för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898478"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Abonnemang och fakturering för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

## <a name="job-collection-plans"></a>Planer för arbetssamling

I Azure Scheduler innehåller en jobbsamling ett visst antal jobb. Jobbsamlingen är den fakturerbara enheten och finns i Standard-, P10 Premium- och P20 Premium-abonnemang, som beskrivs här: 

| Plan för jobbinsamling | Max jobb per samling | Max upprepning | Max jobbsamlingar per prenumeration | Begränsningar | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 jobb per samling | En per minut. Kan inte köra jobb oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 100 standardjobbsamlingar. | Tillgång till Scheduler fullständig funktionsuppsättning | 
| **P10-premie** | 50 jobb per samling | En per minut. Kan inte köra jobb oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 10 000 P10 Premium-jobbsamlingar. För fler samlingar, <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>. | Tillgång till Scheduler fullständig funktionsuppsättning |
| **P20-premie** | 1000 jobb per samling | En per minut. Kan inte köra jobb oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 5 000 P20 Premium-jobbsamlingar. För fler samlingar, <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>. | Tillgång till Scheduler fullständig funktionsuppsättning |
|||||| 

## <a name="pricing"></a>Prissättning

Prisinformation finns i [Schemaläggarprissättning](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Uppgradera eller nedgradera planer

Du kan när som helst uppgradera eller nedgradera en jobbinsamlingsplan i standard-, P10 Premium- och P20 Premium-abonnemangen.

## <a name="active-status-and-billing"></a>Aktiv status och fakturering

Jobbsamlingar är alltid aktiva om inte hela din Azure-prenumeration går in i ett tillfälligt inaktiverat tillstånd på grund av faktureringsproblem. Och även om du kan inaktivera alla jobb i en jobbsamling via en enda åtgärd, ändrar den här åtgärden inte jobbsamlingens faktureringsstatus, så jobbsamlingen faktureras *fortfarande.* Tomma jobbsamlingar anses vara aktiva och faktureras.

Om du vill vara säker på att en jobbsamling inte faktureras måste du ta bort jobbsamlingen.

## <a name="standard-billable-units"></a>Fakturerbara standardenheter

En standard fakturerbar enhet kan ha upp till 10 standardjobbsamlingar. Eftersom en standardjobbsamling kan ha upp till 50 jobb per samling, kan din Standardfaktureringsenhet ha upp till 500 jobb eller upp till nästan 22 *miljoner* jobbkörningar per månad. I den här listan beskrivs hur du faktureras baserat på olika antal standardjobbsamlingar:

* Om du har mellan 1 och 10 standardjobbsamlingar debiteras du för en standardfaktureringsenhet. 

* Om du har mellan 11 och 20 standardjobbsamlingar debiteras du för två vanliga faktureringsenheter. 

* Om du har mellan 21 och 30 standardjobbsamlingar debiteras du för tre vanliga faktureringsenheter och så vidare.

## <a name="p10-premium-billable-units"></a>P10 premium fakturerbara enheter

En P10 premium fakturerbar enhet kan ha upp till 10.000 P10 Premium jobbsamlingar. Eftersom en P10 Premium-jobbsamling kan ha upp till 50 jobb per samling kan din Azure-prenumeration med en P10-premiumfaktureringsenhet ha upp till 500 000 jobb eller upp till nästan 22 *miljarder* jobbkörningar per månad. 

P10 Premium-jobbsamlingar har samma funktioner som standardjobbsamlingar men erbjuder en prispaus för appar som kräver många jobbsamlingar och ger mer skalbarhet. I den här listan beskrivs hur du faktureras baserat på olika antal P10 Premium-jobbsamlingar:

* Om du har mellan 1 och 10 000 P10 Premium-jobbsamlingar debiteras du för en P10-premiumfaktureringsenhet. 

* Om du har mellan 10 001 och 20 000 P10 Premium-jobbsamlingar debiteras du för 2 P10-premiumfaktureringsenheter och så vidare.

## <a name="p20-premium-billable-units"></a>P20 premium fakturerbara enheter

En P20 premium fakturerbar enhet kan ha upp till 5.000 P20 Premium jobbsamlingar. Eftersom en P20 Premium-jobbsamling kan ha upp till 1 000 jobb per jobbsamling, kan din P20-premiumfaktureringsenhet ha upp till 5 000 000 jobb eller upp till nästan 220 *miljarder* jobbkörningar per månad.

P20 Premium-jobbsamlingar har samma funktioner som P10 Premium-jobbsamlingar men stöder också ett större antal jobb per samling och ett större totalt antal jobb totalt sett än P10 Premium, vilket ger mer skalbarhet.

## <a name="plan-comparison"></a>Planera jämförelse

* Om du har mer än 100 standardjobbsamlingar (10 vanliga faktureringsenheter) kan du få ett bättre avtal genom att ha alla jobbsamlingar i en Premium-plan.

* Om du har en standardjobbsamling och en Premium-jobbsamling debiteras du för en standardfaktureringsenhet *och* en premiumfaktureringsenhet.

  Serviceräkningarna scheduler baseras på antalet aktiva jobbsamlingar som är standard eller premium.

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)