---
title: Tillgänglighet och konsekvens i Händelsehubbar i Azure | Microsoft Docs
description: Så här ger maximal mängd tillgänglighet och konsekvens med Händelsehubbar i Azure med hjälp av partitioner.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: e119406292ca1d805f831bc65e3ae6e583147c6d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700697"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Händelsehubbar

## <a name="overview"></a>Översikt
Händelsehubbar i Azure använder en [partitionering modellen](event-hubs-features.md#partitions) för bättre tillgänglighet och parallellisering inom en enskild händelsehubb. Till exempel om en händelsehubb har fyra partitioner och en av dessa partitioner flyttas från en server till en annan i en åtgärd för belastningsutjämning, du fortfarande skicka och ta emot från tre partitioner. Dessutom kan med fler partitioner du behöva flera samtidiga läsare bearbetning av dina data, förbättra din sammanställda genomflöde. Förstå konsekvenserna av att partitionering och ordning i ett distribuerat system är en kritisk del av lösningen.

För att visa en kompromiss mellan ordning och tillgänglighet, finns det [CAP-sats](https://en.wikipedia.org/wiki/CAP_theorem), även kallad Brewers-sats. Den här sats beskrivs valet mellan konsekvens, tillgänglighet och tolerans för partitionen. Det tillstånd som för de system som har partitionerats med nätverket finns alltid förhållandet mellan konsekvens och tillgänglighet.

Brewers sats definierar konsekvens och tillgänglighet på följande sätt:
* Partitionera tolerans: möjlighet för databearbetning systemet fortsätta databearbetning, även om en partition fel inträffar.
* Tillgänglighet: en icke-misslyckas nod returnerar ett rimligt svar inom en rimlig tidsperiod (med några fel eller timeout).
* Konsekvenskontroll: Läs garanteras att returnera den senaste skrivningen för en viss klient.

## <a name="partition-tolerance"></a>Tolerans för partition
Händelsehubbar är byggt på en partitionerad datamodell. Du kan konfigurera antalet partitioner i din event hub under installationen, men du kan inte ändra det här värdet senare. Eftersom du måste använda partitioner med Händelsehubbar, måste du fatta ett beslut om tillgänglighet och konsekvens för ditt program.

## <a name="availability"></a>Tillgänglighet
Det enklaste sättet att komma igång med Händelsehubbar är att använda standardinställningen. Om du skapar en ny **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objekt och använda den **[skicka](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metoden händelserna automatiskt distribueras mellan partitioner i din event hub. Detta gör att störst upptid.

Den här modellen är prioriterade för användningsområden som kräver högsta tid.

## <a name="consistency"></a>Konsekvens
I vissa situationer kan det vara viktigt att sorteringen av händelser. Du kan exempelvis vilja backend-systemet att bearbeta ett uppdateringskommando innan ett borttagningskommando. I den här instansen, du kan ange Partitionsnyckeln på en händelse eller Använd en `PartitionSender` objekt att endast skicka händelser till en viss partition. På så sätt att när dessa händelser läses från partitionen är de läsas i ordning.

Kom ihåg att om viss partitionen som du skickar är tillgänglig, visas ett felsvar med den här konfigurationen. Om du inte har en mappning till en enskild partition som en jämförelsepunkt skickar din händelse till nästa tillgängliga partition händelsehubbtjänsten.

En möjlig lösning så ordning när också maximera upptid, att aggregera händelser som en del av programmet för händelsebearbetning. Det enklaste sättet att göra detta är att klona en händelse med en anpassad sequence number-egenskapen. Följande kod visar ett exempel:

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

Det här exemplet skickar din händelse till en av de tillgängliga partitionerna i din event hub och anger motsvarande sekvensnumret från ditt program. Denna lösning kräver tillstånd hållas av ditt program för bearbetning, men ger en slutpunkt som är mer troligt att vara tillgänglig för din avsändare.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs service](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
