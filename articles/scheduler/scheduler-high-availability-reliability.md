---
title: Hög tillgänglighet och tillförlitlighet – Azure Scheduler
description: Lär dig mer om hög tillgänglighet och tillförlitlighet i Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 3cc15d173ad735d77505f636bd230e0876371271
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300936"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hög tillgänglighet och tillförlitlighet för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 

Azure Scheduler ger både [hög tillgänglighet](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) och tillförlitlighet för dina jobb. Mer information finns i [SLA för Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Hög tillgänglighet

Azure Scheduler är [hög tillgängligt] och använder både Geo-redundant tjänst distribution och geo-regional jobb-replikering.

### <a name="geo-redundant-service-deployment"></a>Distribution av Geo-redundant tjänst

Azure Scheduler är tillgängligt i Azure Portal i nästan [alla geografiska regioner som stöds av Azure idag](https://azure.microsoft.com/global-infrastructure/regions/#services). Så om ett Azure-datacenter i en värdbaserad region blir otillgängligt kan du fortfarande använda Azure Scheduler, eftersom tjänstens funktioner för redundans gör Scheduler tillgänglig från ett annat data Center.

### <a name="geo-regional-job-replication"></a>Geo-regional jobb-replikering

Dina egna jobb i Azure Scheduler replikeras i Azure-regioner. Så om en region har ett avbrott växlar Azure Scheduler över och ser till att jobbet körs från ett annat data Center i det kopplade geografiska området.

Om du till exempel skapar ett jobb i södra centrala USA replikerar Azure Scheduler automatiskt jobbet i norra centrala USA. Om ett haveri inträffar i södra centrala USA kör Azure Scheduler jobbet i norra centrala USA. 

![Geo-regional jobb-replikering](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler ser också till att dina data förblir i samma men bredare geografisk region, bara om ett haveri inträffar i Azure. Så du behöver inte duplicera jobben när du bara vill ha hög tillgänglighet. Azure Scheduler tillhandahåller automatiskt hög tillgänglighet för dina jobb.

## <a name="reliability"></a>Tillförlitlighet

Azure Scheduler garanterar sin egen hög tillgänglighet men använder en annan metod för jobb som skapats av användare. Anta till exempel att jobbet anropar en HTTP-slutpunkt som inte är tillgänglig. Azure Scheduler försöker fortfarande köra jobbet genom att ge dig alternativa sätt att hantera fel: 

* Konfigurera principer för återförsök.
* Konfigurera alternativa slut punkter.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Principer för nya försök

Med Azure Scheduler kan du konfigurera principer för återförsök. Om ett jobb Miss lyckas, försöker Schemaläggaren utföra jobbet fyra gånger med 30 sekunders intervall. Du kan göra den här principen för återförsök mer aggressiv, till exempel 10 gånger med 30 sekunders intervall eller mindre aggressiv, till exempel två gånger med dagliga intervall.

Anta till exempel att du skapar ett veckovis jobb som anropar en HTTP-slutpunkt. Om HTTP-slutpunkten blir otillgänglig under några timmar när jobbet körs, kanske du inte vill vänta i en vecka om att jobbet ska köras igen, vilket inträffar eftersom standard principen för återförsök inte fungerar i det här fallet. Du kanske vill ändra standard principen för återförsök så att återförsök sker, till exempel var tredje timme, i stället för var 30: e sekund. 

Information om hur du konfigurerar en princip för återförsök finns i [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativa slut punkter

Om ditt Azure Scheduler-jobb anropar en slut punkt som inte kan frigöras, även efter att du har återförsöks principen, återgår Scheduler till en alternativ slut punkt som kan hantera sådana fel. Om du ställer in den här slut punkten anropar Scheduler den slut punkten, vilket gör dina egna jobb hög tillgängliga när felen inträffar.

Det här diagrammet visar till exempel hur Schemaläggaren följer policyn för återförsök vid anrop till en webb tjänst i New York. Om försöken inte fungerar kontrollerar Scheduler om det finns en alternativ slut punkt. Om slut punkten finns börjar Scheduler skicka begär anden till den alternativa slut punkten. Samma återförsöks policy gäller för både den ursprungliga åtgärden och den alternativa åtgärden.

![Scheduler-beteende med principer för återförsök och alternativ slut punkt](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Åtgärds typen för den alternativa åtgärden kan skilja sig från den ursprungliga åtgärden. Till exempel, även om den ursprungliga åtgärden anropar en HTTP-slutpunkt, kan den alternativa åtgärden Logga fel med hjälp av en lagrings kö, Service Bus kö eller åtgärd för Service Bus ämnet.

Information om hur du konfigurerar en alternativ slut punkt finns i [ErrorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Skapa komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md)
* [Gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)
