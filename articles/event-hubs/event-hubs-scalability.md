---
title: Skalbarhet – Azure-händelsehubbar | Microsoft-dokument
description: Den här artikeln innehåller information om hur du skalar Azure Event Hubs med hjälp av partitioner och dataflödesenheter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280955"
---
# <a name="scaling-with-event-hubs"></a>Skala med händelsehubbar

Det finns två faktorer som påverkar skalning med Event Hubs.
*   Genomflödesenheter
*   Partitioner

## <a name="throughput-units"></a>Genomflödesenheter

Dataflödeskapaciteten för Event Hubs styrs av *dataflödesenheter*. Genomflödesenheter är färdiga kapacitetsenheter. Med ett enda dataflöde kan du:

* Inträngning: Upp till 1 MB per sekund eller 1000 händelser per sekund (beroende på vilket som inträffar först).
* Utgående: Upp till 2 MB per sekund eller 4096 händelser per sekund.

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera genomflödesenheter på bladet **Skala** för namnområdena på [Azure-portalen](https://portal.azure.com). Du kan också hantera dataflödesenheter programmässigt med hjälp av [API:erna för eventhubbar.](event-hubs-api-overview.md)

Dataflödesenheter är förköpta och faktureras per timme. När de väl har köpts debiteras de för minst en timme. Upp till 20 dataflödesenheter kan köpas för ett namnområde för eventhubbar och delas över alla händelsehubbar i namnområdet.

Funktionen **För automatisk uppblåsning** i Event Hubs skalas automatiskt upp genom att öka antalet dataflödesenheter för att uppfylla användningsbehoven. Genomströmningsenheter förhindrar begränsningsscenarier, där:

- Datainträngningshastigheten överskrider inställda dataflödesenheter.
- Datautgående begäranhetsfrekvens överskrider angivna dataflödesenheter.

Event Hubs-tjänsten ökar dataflödet när belastningen ökar utöver minimitröskeln, utan att några begäranden misslyckas med ServerBusy-fel. 

Mer information om funktionen för automatisk uppblåsning finns i [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partitionsnyckeln

Du kan använda en [partitionsnyckel](event-hubs-programming-guide.md#partition-key) för att mappa inkommande händelsedata till specifika partitioner för dataorganisationen. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Den bearbetas via en statisk hash-funktion som skapar partitionstilldelningen. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen.

Händelseutfärdaren känner bara till sin partitionsnyckel, inte den partition som händelserna publiceras till. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition.


## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md)
- [Översikt över tjänsten Event Hubs](event-hubs-what-is-event-hubs.md)
