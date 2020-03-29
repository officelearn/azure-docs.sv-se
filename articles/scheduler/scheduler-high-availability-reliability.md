---
title: Hög tillgänglighet och tillförlitlighet
description: Lär dig mer om hög tillgänglighet och tillförlitlighet i Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898562"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hög tillgänglighet och tillförlitlighet för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

Azure Scheduler ger både [hög tillgänglighet](https://docs.microsoft.com/azure/architecture/framework/#resiliency) och tillförlitlighet för dina jobb. Mer information finns i [SLA för Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Hög tillgänglighet

Azure Scheduler är [mycket tillgänglig] och använder både geo-redundant tjänstdistribution och geo-regional jobbreplikering.

### <a name="geo-redundant-service-deployment"></a>Geo-redundant tjänstdistribution

Azure Scheduler är tillgängligt i nästan [alla geografiska regioner som stöds av Azure idag](https://azure.microsoft.com/global-infrastructure/regions/#services). Så om ett Azure-datacenter i en värdregion blir otillgängligt kan du fortfarande använda Azure Scheduler eftersom tjänstens redundansfunktioner gör Scheduler tillgängligt från ett annat datacenter.

### <a name="geo-regional-job-replication"></a>Georegional jobbreplikering

Dina egna jobb i Azure Scheduler replikeras över Azure-regioner. Så om en region har ett avbrott, misslyckas Azure Scheduler över och ser till att ditt jobb körs från ett annat datacenter i den parade geografiska regionen.

Om du till exempel skapar ett jobb i Södra centrala USA replikerar Azure Scheduler automatiskt det jobbet i norra centrala USA. Om ett fel inträffar i södra centrala USA kör Azure Scheduler jobbet i norra centrala USA. 

![Georegional jobbreplikering](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler ser också till att dina data förblir inom samma men bredare geografiska region, ifall ett fel skulle inträffar i Azure. Så du behöver inte duplicera dina jobb när du bara vill ha hög tillgänglighet. Azure Scheduler ger automatiskt hög tillgänglighet för dina jobb.

## <a name="reliability"></a>Tillförlitlighet

Azure Scheduler garanterar sin egen hög tillgänglighet men har en annan metod för användarskapade jobb. Anta till exempel att jobbet anropar en HTTP-slutpunkt som inte är tillgänglig. Azure Scheduler försöker fortfarande köra jobbet genom att ge dig alternativa sätt att hantera fel: 

* Ställ in principer för återförsök.
* Ställ in alternativa slutpunkter.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Återförsöksprinciper

Med Azure Scheduler kan du ställa in principer för återförsök. Om ett jobb misslyckas försöker Scheduler som standard jobbet fyra gånger med 30 sekunders intervall. Du kan göra den här återförsöksprincipen mer aggressiv, till exempel 10 gånger med 30 sekunders intervall eller mindre aggressiv, till exempel två gånger med dagliga intervall.

Anta till exempel att du skapar ett veckojobb som anropar en HTTP-slutpunkt. Om HTTP-slutpunkten blir otillgänglig under några timmar när jobbet körs kanske du inte vill vänta en vecka till på att jobbet ska köras igen, vilket inträffar eftersom standardprincipen för återförsök inte fungerar i det här fallet. Så du kanske vill ändra standard återförsöksprincipen så att försök sker, till exempel var tredje timme, i stället för var 30:e sekund. 

Mer information om hur du konfigurerar en återförsöksprincip finns i [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativa slutpunkter

Om ditt Azure Scheduler-jobb anropar en slutpunkt som inte kan nås, även efter att ha följt återförsöksprincipen, faller Scheduler tillbaka till en alternativ slutpunkt som kan hantera sådana fel. Så om du ställer in den här slutpunkten anropar Scheduler slutpunkten, vilket gör dina egna jobb högtillgängliga när fel inträffar.

Det här diagrammet visar till exempel hur Scheduler följer återförsöksprincipen när du anropar en webbtjänst i New York. Om försöken misslyckas söker Scheduler efter en alternativ slutpunkt. Om slutpunkten finns börjar Scheduler skicka begäranden till den alternativa slutpunkten. Samma återförsöksprincip gäller både den ursprungliga åtgärden och den alternativa åtgärden.

![Scheduler-beteende med återförsöksprincip och alternativ slutpunkt](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Åtgärdstypen för den alternativa åtgärden kan skilja sig från den ursprungliga åtgärden. Även om den ursprungliga åtgärden till exempel anropar en HTTP-slutpunkt kan den alternativa åtgärden logga fel med hjälp av en lagringskö, servicebusskö eller servicebussavsnittsåtgärd.

Mer information om hur du konfigurerar en alternativ slutpunkt finns i [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
* [Gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)
