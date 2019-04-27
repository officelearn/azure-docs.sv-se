---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft Docs
description: Så här att ange den maximala mängden tillgänglighet och konsekvens med Azure Event Hubs med partitioner.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e5cad797b633d43bcc9ead657a60fca8aa6679bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822387"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs

## <a name="overview"></a>Översikt
Azure Event Hubs använder en [partitionering modellen](event-hubs-features.md#partitions) för bättre tillgänglighet och parallellisering inom en enda händelsehubb. Till exempel det så att om en händelsehubb har fyra partitioner, och en av dessa partitioner har flyttats från en server till en annan i en belastningsutjämningsåtgärd, kan du fortfarande skicka och ta emot från tre andra partitioner. Dessutom kan med fler partitioner du ha flera samtidiga läsare bearbetning av dina data, förbättra dina aggregerat dataflöde. Förstå konsekvenserna av partitionering och ordning i ett distribuerat system är en viktig aspekt av lösningsdesign.

För att förklara avvägningen mellan ordning och tillgänglighet kan se den [CAP-satsen](https://en.wikipedia.org/wiki/CAP_theorem), även kallad Brewers-satsen. Den här satsen beskrivs valet mellan konsekvens, tillgänglighet och tolerans för partitionen. Det säger att för system partitioneras efter nätverk finns alltid kompromiss mellan konsekvens och tillgänglighet.

Brewers satsen definierar konsekvens och tillgänglighet på följande sätt:
* Partitionera tolerans: möjligheten för ett system för databearbetning att fortsätta bearbetningsdata även om en partition fel inträffar.
* Tillgänglighet: en icke-misslyckas nod returnerar ett rimligt svar inom en rimlig tidsperiod (med några fel eller timeout-fel).
* Konsekvens: en läsning garanteras att returnera den senaste skrivningen för en viss klient.

## <a name="partition-tolerance"></a>Tolerans för partition
Händelsehubbar är byggt på en modell med partitionerade data. Du kan konfigurera antalet partitioner i din event hub under installationen, men du kan inte ändra det här värdet senare. Eftersom du måste använda partitioner med Event Hubs, måste du fatta ett beslut om tillgänglighet och konsekvens för ditt program.

## <a name="availability"></a>Tillgänglighet
Det enklaste sättet att komma igång med Event Hubs är att använda standardbeteendet. Om du skapar en ny **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objektet och använda den **[skicka](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metod, distribueras automatiskt dina händelser mellan partitioner i event hub. Det här beteendet kan störst drifttid.

Den här modellen är prioriterade för användningsfall som kräver högsta drifttid.

## <a name="consistency"></a>Konsekvens
I vissa situationer kan det vara viktigt att sorteringen av händelser. Exempelvis kan du dina backend-system att bearbeta ett uppdateringskommando innan ett borttagningskommando. I den här instansen, du kan ange Partitionsnyckeln för en händelse, eller använda en `PartitionSender` objekt att endast skicka händelser till en viss partition. Detta innebär att när dessa händelser läses från partitionen, är de läsas i ordning.

Kom ihåg att om viss partitionen som du skickar är tillgänglig, du får ett felsvar med den här konfigurationen. Om du inte har en mappning till en enskild partition som en jämförelsepunkt skickar din händelse till nästa tillgängliga partition händelsehubbtjänsten.

En möjlig lösning för att säkerställa ordning, och även maximera drifttid, är att sammanfatta händelser som en del av ditt program för händelsebearbetning. Det enklaste sättet att göra detta är att stämpla evenemanget med en anpassad sequence number-egenskapen. Följande kod visar ett exempel:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

Det här exemplet skickar din händelse till en av de tillgängliga partitionerna i event hub och anger motsvarande sekvensnumret från ditt program. Den här lösningen kräver tillstånd och kan inte hållas programmets bearbetning, men ger din avsändare en slutpunkt som är mer troligt att vara tillgängliga.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs-tjänsten](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
