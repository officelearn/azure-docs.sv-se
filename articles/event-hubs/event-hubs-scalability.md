---
title: Skalbarhet – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du skalar Azure-Event Hubs med partitioner och data flödes enheter.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6d1c7bc5a0206b634e5a85243f25b2c926762d7e
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315477"
---
# <a name="scaling-with-event-hubs"></a>Skalning med Event Hubs

Det finns två faktorer som påverkar skalning med Event Hubs.
*   Genomflödesenheter
*   Partitioner

## <a name="throughput-units"></a>Genomflödesenheter

Data flödes kapaciteten för Event Hubs styrs av *data flödes enheter*. Genomflödesenheter är färdiga kapacitetsenheter. Med ett enda data flöde kan du:

* Ingress: upp till 1 MB per sekund eller 1000 händelser per sekund (beroende på vilket som kommer först).
* Utgående: upp till 2 MB per sekund eller 4096 händelser per sekund.

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera genomflödesenheter på bladet **Skala** för namnområdena på [Azure-portalen](https://portal.azure.com). Du kan också hantera data flödes enheter program mässigt med hjälp av [Event Hubs API: er](event-hubs-api-overview.md).

Data flödes enheter köps och faktureras per timme. När de väl har köpts debiteras de för minst en timme. Upp till 20 data flödes enheter kan köpas för ett Event Hubs-namnområde och delas över alla händelse nav i namn området.

Funktionen för **Automatisk** ökning i Event Hubs skalas automatiskt upp genom att öka antalet data flödes enheter för att möta användnings behoven. Ökande data flödes enheter förhindrar begränsnings scenarier där:

- Data ingångs frekvensen överskrider den angivna data flödes enheten.
- Taxan för utgående begär Anden överskrider mängd enheter för data flöde.

Event Hubs tjänsten ökar data flödet när belastningen ökar utöver minimi tröskelvärdet, utan att begär Anden som misslyckats med ServerBusy-fel uppstår. 

Mer information om funktionen för automatiskt öknings funktionen finns i [skala data flödes enheter automatiskt](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partitionsnyckeln

Du kan använda en [partitionsnyckel](event-hubs-programming-guide.md#partition-key) för att mappa inkommande händelse data till vissa partitioner för data organisationens syfte. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Den bearbetas via en statisk hash-funktion som skapar partitionstilldelningen. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen.

Händelseutfärdaren känner bara till sin partitionsnyckel, inte den partition som händelserna publiceras till. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition.


## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md)
- [Översikt över Event Hubs tjänsten](event-hubs-what-is-event-hubs.md)
