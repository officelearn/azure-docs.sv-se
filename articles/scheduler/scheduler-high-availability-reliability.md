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
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533391"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hög tillgänglighet och tillförlitlighet för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler erbjuder både [hög tillgänglighet](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) och tillförlitligheten för dina jobb. Mer information finns i [SLA för Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Hög tillgänglighet

Azure Scheduler är [högtillgänglig] och använder både tjänstdistributionen geo-redundant och geo-regionala jobbet replikering.

### <a name="geo-redundant-service-deployment"></a>GEO-redundant tjänstdistribution

Azure Scheduler är tillgängliga i Azure-portalen i nästan [varje geografisk region som stöds av Azure i dag](https://azure.microsoft.com/global-infrastructure/regions/#services). Så om ett Azure-datacenter i en värdbaserad region blir otillgänglig, kan du fortfarande använda Azure Scheduler eftersom tjänstens växling tillgängliggöra Scheduler från ett annat datacenter.

### <a name="geo-regional-job-replication"></a>GEO-regionala jobbet replikering

Dina egna jobb i Azure Scheduler replikeras i Azure-regioner. Så om en region har ett avbrott, Azure Scheduler växlas över och ser till att jobbet körs från ett annat datacenter i parad geografiska område.

Till exempel om du skapar ett jobb i södra centrala USA, replikering Azure Scheduler automatisk av jobbet i norra centrala USA. Om det inträffar ett fel i södra centrala USA, körs jobbet i Azure Scheduler i norra centrala USA. 

![GEO-regionala jobbet replikering](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler gör också att dina data håller sig inom samma men bredare geografisk region, i händelse av ett programvarufel i Azure. Därför har du inte duplicera dina jobb när du bara vill ha hög tillgänglighet. Azure Scheduler ger automatiskt hög tillgänglighet för dina jobb.

## <a name="reliability"></a>Tillförlitlighet

Azure Scheduler garanterar en egen hög tillgänglighet men tar en annan metod för jobben som skapats av användare. Anta exempelvis att jobbet anropar en HTTP-slutpunkt som inte är tillgänglig. Azure Scheduler försöker fortfarande att kunna köras ditt jobb genom att ge alternativa metoder för att hantera fel: 

* Konfigurera principer för återförsök.
* Konfigurera alternativa slutpunkter.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Principer för nya försök

Azure Scheduler kan du konfigurera principer för återförsök. Om det inte går, sedan som standard försöker Scheduler jobbet fyra gånger med 30 sekunders mellanrum. Du kan göra en sådan återförsöksprincip mer Aggressivt, till exempel 10 gånger med 30 sekunders mellanrum eller mindre aggressiva, till exempel två gånger på varje dag intervall.

Anta exempelvis att du skapar ett veckovis jobb som anropar en HTTP-slutpunkt. Om HTTP-slutpunkten är tillgänglig i några timmar när jobbet körs kanske vill du inte vänta ytterligare en vecka för att jobbet ska köras igen, vilket inträffar eftersom standardprincipen för återförsök inte fungerar i det här fallet. Därför kan du ändra återförsöksprincipen som standard så att nya försök inträffa, till exempel var tredje timme, snarare än var 30: e sekund. 

Läs hur du ställer in en återförsöksprincip i [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativa slutpunkter

Om ditt Azure Scheduler-jobb kräver att en slutpunkt som inte kan nås, även när du har genomfört återförsöksprincipen, använder Scheduler till en annan slutpunkt som kan hantera sådana fel. Om du har konfigurerat den här slutpunkten anropar Scheduler så att slutpunkten, vilket gör dina egna jobb med hög tillgänglighet när fel uppstår.

Det här diagrammet visar till exempel hur Scheduler följer återförsöksprincipen när du anropar en webbtjänst i New York. Om återförsöken misslyckas kontrollerar Scheduler för en annan slutpunkt. Om slutpunkten finns Scheduler startar begäranden skickas till den alternativa slutpunkten. Samma återförsöksprincipen gäller för både den ursprungliga åtgärden och den alternativa åtgärden.

![Scheduler beteende med återförsöksprincipen och alternativa slutpunkten](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Åtgärdstyp för åtgärden alternativa kan skilja sig från den ursprungliga åtgärden. Även om den ursprungliga åtgärden kräver att en HTTP-slutpunkt, kan åtgärden alternativa exempelvis logga fel med en lagringskö, Service Bus-kö eller Service Bus-ämnesåtgärd.

Läs hur du ställer in en annan slutpunkt i [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Skapa komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md)
* [Gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)
