---
title: Skalbarhet – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du skalar Azure-Event Hubs med partitioner och data flödes enheter.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280955"
---
# <a name="scaling-with-event-hubs"></a>Skalning med Event Hubs

Det finns två faktorer som påverkar skalning med Event Hubs.
*   Genomflödesenheter
*   Partitioner

## <a name="throughput-units"></a>Genomflödesenheter

Genomflödeskapaciteten i Event Hubs styrs av *genomflödesenheter*. Genomflödesenheter är färdiga kapacitetsenheter. Med ett enda data flöde kan du:

* Ingång: Upp till 1 MB per sekund eller 1 000 händelser per sekund (beroende på vilket som inträffar först).
* Utgång: Upp till 2 MB per sekund eller 4096 händelser per sekund.

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera data flödes enheter på bladet **skala** i namn områdena i [Azure Portal](https://portal.azure.com). Du kan också hantera data flödes enheter program mässigt med hjälp av [Event Hubs API: er](event-hubs-api-overview.md).

Genomflödesenheter är förköpta och faktureras per timme. När de väl har köpts debiteras de för minst en timme. Upp till 20 genomflödesenheter enheter kan köpas för ett namnområde för Event Hubs och delas över alla händelsehubbar i det namnområdet.

Funktionen för **Automatisk** ökning i Event Hubs skalas automatiskt upp genom att öka antalet data flödes enheter för att möta användnings behoven. Öka genomflödesenheter förhindrar begränsningsscenarier där:

- Ingående datataxa överskrida set-dataflödesenheter.
- Datataxa utgående begäran överskrider set-dataflödesenheter.

Händelsehubbtjänsten ökar dataflödet när belastningen ökar den minsta överskrids utan några förfrågningar som misslyckas med ServerBusy fel. 

Mer information om funktionen för automatiskt öknings funktionen finns i [skala data flödes enheter automatiskt](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partitionsnyckeln

Du kan organisera data med hjälp av en [partitionsnyckel](event-hubs-programming-guide.md#partition-key) som mappar inkommande händelsedata till specifika partitioner. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Den bearbetas via en statisk hash-funktion som skapar partitionstilldelningen. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen.

Händelseutfärdaren känner bara till sin partitionsnyckel, inte den partition som händelserna publiceras till. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition.


## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md)
- [Översikt över Event Hubs tjänsten](event-hubs-what-is-event-hubs.md)
