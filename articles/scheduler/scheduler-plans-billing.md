---
title: Planer och fakturering – Azure Scheduler
description: Lär dig mer om prenumerationer och fakturering för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344316"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planer och fakturering för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>Jobbet samlingsplaner

En jobbsamling innehåller ett visst antal jobb i Azure Scheduler. Jobbsamlingen är fakturerbar enhet och ingår i Standard, P10 Premium eller P20 Premium-planer, som beskrivs här: 

| Samlingsplan för jobbet | Max antal jobb per samling | Max antal upprepningar | Maximalt antal jobbsamlingar per prenumeration | Limits | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 jobb per samling | En per minut. Jobb kan inte köras oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 100 jobbsamlingar som Standard. | Åtkomst till fullständiga funktionsuppsättningen för Scheduler | 
| **P10 Premium** | 50 jobb per samling | En per minut. Jobb kan inte köras oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 10 000 P10 Premium-jobbsamlingar. För mer samlingar <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>. | Åtkomst till fullständiga funktionsuppsättningen för Scheduler |
| **P20 Premium** | 1000 jobb per samling | En per minut. Jobb kan inte köras oftare än en per minut. | Varje Azure-prenumeration kan ha upp till 5 000 P20 Premium-jobbsamlingar. För mer samlingar <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>. | Åtkomst till fullständiga funktionsuppsättningen för Scheduler |
|||||| 

## <a name="pricing"></a>Prissättning

Information om prissättning finns i [Scheduler priser](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Uppgradera eller nedgradera planer

Du kan uppgradera eller nedgradera en jobbet samlingsplanen mellan Standard, P10 Premium eller P20 Premium-planer när som helst.

## <a name="active-status-and-billing"></a>Status för aktiva och fakturering

Jobbsamlingar är alltid aktiv, såvida inte hela Azure-prenumerationen hamnar i ett tillfälligt inaktiverat tillstånd på grund av fakturaärenden. Och även om du inaktivera alla jobb i en jobbsamling via en enda åtgärd, den här åtgärden inte ändras status för jobb-samling fakturering så jobbsamlingen är *fortfarande* faktureras. Tom jobbsamlingar betraktas active och faktureras.

För att säkerställa en jobbsamling inte är faktureras, måste du ta bort jobbsamlingen.

## <a name="standard-billable-units"></a>Standard faktureringsbara enheter

En standardenhet för fakturerbar kan ha upp till 10 Standard jobbsamlingar. Eftersom en jobbsamling som Standard kan ha upp till 50 jobb per samling, en standardenhet för fakturering kan din Azure-prenumeration har upp till 500 jobb eller upp till nästan 22 *miljoner* jobbkörningar per månad. Den här listan förklaras hur du faktureras baserat på olika mängder Standard jobbsamlingar:

* Om du har mellan 1 och 10 Standard jobbsamlingar kan faktureras du för en standardenhet för fakturering. 

* Om du har mellan 11 och 20 Standard jobbsamlingar faktureras du för två standardenheter för fakturering. 

* Om du har mellan 21 och 30 Standard jobbsamlingar du faktureras för tre standardenheter för fakturering och så vidare.

## <a name="p10-premium-billable-units"></a>Premium P10-faktureringsbara enheter

En P10 premium faktureringsbar enhet kan ha upp till 10 000 P10 Premium-jobbsamlingar. Eftersom en P10 Premium jobbsamling kan ha upp till 50 jobb per samling, en P10 premium faktureringsenheten kan din Azure-prenumeration har upp till 500 000 jobb eller upp till nästan 22 *miljard* jobbkörningar per månad. 

P10 Premium-jobbsamlingar innehåller samma funktioner som Standard jobbsamlingar, men erbjuder en prisskillnad för appar som kräver många jobbsamlingar och ge mer skalbarhet. Den här listan förklaras hur du faktureras baserat på olika mängder P10 Premium-jobbsamlingar:

* Om du har mellan 1 och 10 000 jobbsamlingar för P10 Premium faktureras du för en P10 premium Faktureringsenhet. 

* Om du har mellan 10,001 och 20 000 P10 Premium-jobbsamlingar du faktureras för 2 P10 premium faktureringsenheter och så vidare.

## <a name="p20-premium-billable-units"></a>P20 premium faktureringsbara enheter

En P20 premium faktureringsbar enhet kan ha upp till 5 000 P20 Premium-jobbsamlingar. Eftersom en P20 Premium jobbsamling kan ha upp till 1 000 jobb per jobbsamling, en P20 premium faktureringsenheten kan din Azure-prenumeration har upp till 5 000 000 jobb eller upp till nästan 220 *miljard* jobbkörningar per månad.

P20 Premium-jobbsamlingar ger samma funktioner som P10 Premium-jobbsamlingar men stöder ett större antal jobb per samling och ett större totala antal jobb övergripande än P10 Premium, erbjuder mer skalbarhet.

## <a name="plan-comparison"></a>Jämförelse av avtal

* Om du har fler än 100 Standard jobbsamlingar (10 standard faktureringsenheter), kan du få en bättre affär genom att låta alla jobbsamlingar i en premiumplan.

* Om du har en jobbsamling som Standard och en Premium-jobbsamlingen kommer du faktureras för en standardenhet för fakturering *och* fakturering premiumenhet.

  Scheduler-tjänsten fakturerar baserat på antalet aktiva jobbsamlingar som är antingen standard eller premium.

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)