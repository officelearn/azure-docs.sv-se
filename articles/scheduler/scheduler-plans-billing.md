---
title: Planer och fakturering i Azure Scheduler
description: Planer och fakturering i Azure Scheduler
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 03f335634b7ce1fe4aa6251d6ec21922ed9b84c8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887495"
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Planer och fakturering i Azure Scheduler
## <a name="job-collection-plans"></a>Jobbet Samlingsplaner
Jobbsamlingar är fakturerbara entiteten i Azure Scheduler. Jobbsamlingar innehåller ett antal jobb och träder i tre planer – Standard, P10 Premium eller P20 Premium – som beskrivs nedan.

| **Samlingsplan för jobbet** | **Max antal jobb per Jobbsamling** | **Max antal upprepningar** | **Maximalt antal Jobbsamlingar per prenumeration** | **Begränsningar** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |50 jobb per jobbsamling |En gång per minut. Det går inte att köra jobb oftare än en gång i minuten |Upp till 100 jobbsamlingar som standard tillåts i en prenumeration |Åtkomst till fullständiga funktionsuppsättningen för Scheduler |
| **P10 Premium** |50 jobb per jobbsamling |En gång per minut. Det går inte att köra jobb oftare än en gång i minuten |En prenumeration kan upp till 10 000 P10 Premium-jobbsamlingar. <a href="mailto:wapteams@microsoft.com">Kontakta oss</a> mer. |Åtkomst till fullständiga funktionsuppsättningen för Scheduler |
| **P20 Premium** |1000 jobb per jobbsamling |En gång per minut. Det går inte att köra jobb oftare än en gång i minuten |En prenumeration kan upp till 10 000 P20 Premium-jobbsamlingar. <a href="mailto:wapteams@microsoft.com">Kontakta oss</a> mer. |Åtkomst till fullständiga funktionsuppsättningen för Scheduler |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>- Och nedgraderingar av jobbet Samlingsplaner
Du kan uppgradera eller nedgradera en jobbet samlingsplanen när som helst mellan Standard, P10 Premium eller P20 Premium-planer.

## <a name="billing-and-azure-plans"></a>Fakturerings- och Azure-prenumerationer
Om du har fler än 100 standard jobbsamlingar (10 standard faktureringsenheter), är det en bättre affär ha alla jobbsamlingar i premium-prenumerationen.

Om du har en jobbsamling som standard och premium för en jobbsamling, du är faktureras en standard faktureringsenheten *och* fakturering premiumenhet. Scheduler-tjänsten räkningarna baserat på antalet aktiva jobbsamlingar som är inställda på antingen standard eller premium; Detta beskrivs närmare i följande två avsnitt.

## <a name="standard-billable-units"></a>Standard faktureringsbara enheter
En standardenhet för fakturerbar kan innehålla upp till 10 standard jobbsamlingar. Eftersom en jobbsamling som standard kan ha upp till 50 jobb per jobbsamling, kan en prenumeration har upp till 500 jobb – upp till nästan 22 miljoner jobbkörningar per månad i en standardenhet för fakturering.

Om du har mellan 1 och 10 standard jobbsamlingar, kommer du att debiteras för en standardenhet för fakturering. Om du har mellan 11 och 20 standard jobbsamlingar, debiteras du för två standardenheter för fakturering. Om du har mellan 21 och 30 standard jobbsamlingar, du debiteras för tre standardenheter för fakturering och så vidare.

## <a name="p10-premium-billable-units"></a>P10 Premium-faktureringsbara enheter
En P10 premium faktureringsbar enhet kan innehålla upp till 10 000 P10 premium-jobbsamlingar. Eftersom en P10 premium jobbsamling kan ha upp till 50 jobb per jobbsamling, kan en premium-faktureringsenheten en prenumeration har upp till 500 000 jobb – upp till nästan 22 miljarder jobbkörningar per månad.

Om du har mellan 1 och 10 000 premium-jobbsamlingar, kommer du att debiteras för en P10 premium Faktureringsenhet. Om du har mellan 10,001 och 20 000 premium-jobbsamlingar, du debiteras för 2 P10 premium faktureringsenheter och så vidare.

Därför P10 premium-jobbsamlingar har samma funktioner som standard jobbsamlingar men ger en prisskillnad om programmet kräver många jobbsamlingar.

## <a name="p20-premium-billable-units"></a>P20 Premium faktureringsbara enheter
En P20 premium faktureringsbar enhet kan innehålla upp till 5 000 P20 premium-jobbsamlingar. Eftersom en P20 premium jobbsamling kan ha upp till 1 000 jobb per jobbsamling, kan en premium-faktureringsenheten en prenumeration har upp till 5 000 000 jobb – upp till nästan 220 miljarder jobbkörningar per månad.

P20 premium-jobbsamlingar innehåller samma funktioner som P10 premium-jobbsamlingar, men också stöder ett större antal jobb per jobbsamling och större totala antal jobb övergripande än P10 premium så att du har mer skalbarhet.

## <a name="billing-and-active-status"></a>Fakturerings- och aktiv Status
Jobbsamlingar är alltid aktiva om inte din hela prenumerationen har gått till vissa tillfälligt inaktiverat tillstånd på grund av fakturering problem. Det enda sättet att se till att en jobbsamling, inte faktureras är att ta bort jobbsamlingen.

Men du kan inaktivera alla jobb i en jobbsamling i en enda åtgärd, ändras inte fakturering status för jobbsamlingen – jobbsamlingen kommer *fortfarande* debiteras. På samma sätt tom jobbsamlingar betraktas active och kommer att debiteras.

## <a name="pricing"></a>Prissättning
Information om prissättning finns i [Scheduler priser](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

