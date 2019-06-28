---
title: Skalbarhet – Azure Event Hubs | Microsoft Docs
description: Innehåller information om hur du skalar Azure Event Hubs.
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
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276787"
---
# <a name="scaling-with-event-hubs"></a>Skala med Händelsehubbar

Det finns två faktorerna som påverkar skala med Händelsehubbar.
*   Genomflödesenheter
*   Partitioner

## <a name="throughput-units"></a>Genomflödesenheter

Genomflödeskapaciteten i Event Hubs styrs av *genomflödesenheter*. Genomflödesenheter är färdiga kapacitetsenheter. Ett enda dataflöde kan du:

* Ingång: Upp till 1 MB per sekund eller 1 000 händelser per sekund (beroende på vilket som inträffar först).
* Utgång: Upp till 2 MB per sekund eller 4096 händelser per sekund.

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera genomflödesenheter på den **skala** bladet för namnområdena på den [Azure-portalen](https://portal.azure.com). Du kan också hantera genomflödesenheter genom programmering med den [API: er för Event Hubs](event-hubs-api-overview.md).

Genomflödesenheter är förköpta och faktureras per timme. När de väl har köpts debiteras de för minst en timme. Upp till 20 genomflödesenheter enheter kan köpas för ett namnområde för Event Hubs och delas över alla händelsehubbar i det namnområdet.

Den **automatisk ökning** i Event Hubs automatiskt skalar upp genom att öka antalet dataflödesenheter, för användning behov. Öka genomflödesenheter förhindrar begränsningsscenarier där:

- Ingående datataxa överskrida set-dataflödesenheter.
- Datataxa utgående begäran överskrider set-dataflödesenheter.

Händelsehubbtjänsten ökar dataflödet när belastningen ökar den minsta överskrids utan några förfrågningar som misslyckas med ServerBusy fel. 

Mer information om den automatisk ökning av funktionen, se [skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitioner

Partitioner kan du skala för din nedströms bearbetning. På grund av modellen konsumentmönster indelat i partitioner som Händelsehubbar erbjuder med partitioner, du kan skala ut vid bearbetning av händelser samtidigt. En Händelsehubb kan ha upp till 32 partitioner.

Vi rekommenderar att du balanserar 1:1-genomflödesenheter och partitioner för att uppnå optimal skala. En enskild partition har en garanterad ingående och utgående på upp till en genomflödesenhet. Du kanske kan uppnå högre dataflöde på en partition, garanteras inte prestanda. Det är därför vi rekommenderar starkt att antalet partitioner i en händelsehubb är större än eller lika med antalet dataflödesenheter.

Det totala dataflödet som du planerar att behöva får du vet antal throughput units som du behöver och det minsta antalet partitioner, men hur många partitioner bör du ha? Välj antal partitioner baserat på den underordnade parallellitet som du vill uppnå samt framtida dataflödet behöver. Det finns ingen kostnad för antalet partitioner som du har i en Händelsehubb.

Utförlig prisinformation för Event Hubs finns i [Priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md)
- [Översikt över Event Hubs-tjänsten](event-hubs-what-is-event-hubs.md)
