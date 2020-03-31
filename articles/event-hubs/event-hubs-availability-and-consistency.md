---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft-dokument
description: Så här anger du den maximala mängden tillgänglighet och konsekvens med Azure Event Hubs med hjälp av partitioner.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904481"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs

## <a name="overview"></a>Översikt
Azure Event Hubs använder en [partitioneringsmodell](event-hubs-scalability.md#partitions) för att förbättra tillgänglighet och parallellisering inom en enda händelsehubb. Om till exempel en händelsenav har fyra partitioner och en av dessa partitioner flyttas från en server till en annan i en belastningsutjämningsåtgärd, kan du fortfarande skicka och ta emot från tre andra partitioner. Med fler partitioner kan du dessutom ha fler samtidiga läsare som bearbetar dina data, vilket förbättrar ditt samlade dataflöde. Att förstå konsekvenserna av partitionering och beställning i ett distribuerat system är en kritisk aspekt av lösningsdesign.

För att förklara avvägningen mellan beställning och tillgänglighet, se [cap-satsen](https://en.wikipedia.org/wiki/CAP_theorem), även känd som Brewer's theorem. I det här satset beskrivs valet mellan konsekvens, tillgänglighet och partitionstolerans. Det anges att för de system som partitioneras av nätverk finns det alltid avvägning mellan konsekvens och tillgänglighet.

Brewers sats definierar konsekvens och tillgänglighet enligt följande:
* Partitionstolerans: möjligheten för ett databehandlingssystem att fortsätta bearbeta data även om ett partitionsfel inträffar.
* Tillgänglighet: En nod som inte misslyckas returnerar ett rimligt svar inom rimlig tid (utan fel eller timeout).
* Konsekvens: en läsning garanteras att returnera den senaste skrivningen för en viss klient.

## <a name="partition-tolerance"></a>Partitionstolerans
Event Hubs bygger ovanpå en partitionerad datamodell. Du kan konfigurera antalet partitioner i händelsehubben under installationen, men du kan inte ändra det här värdet senare. Eftersom du måste använda partitioner med eventhubbar måste du fatta ett beslut om tillgänglighet och konsekvens för ditt program.

## <a name="availability"></a>Tillgänglighet
Det enklaste sättet att komma igång med Event Hubs är att använda standardbeteendet. Om du skapar ett nytt **[EventHubClient-objekt](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** och använder metoden **[Skicka](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** fördelas dina händelser automatiskt mellan partitioner i händelsehubben. Detta gör det möjligt för den största upptiden.

För användningsfall som kräver maximal upptid är den här modellen att föredra.

## <a name="consistency"></a>Konsekvens
I vissa fall kan ordning och reda på händelser vara viktigt. Du kanske till exempel vill att backend-systemet ska bearbeta ett uppdateringskommando innan ett borttagningskommando. I det här fallet kan du antingen ställa in `PartitionSender` partitionsnyckeln på en händelse eller använda ett objekt för att bara skicka händelser till en viss partition. Detta säkerställer att när dessa händelser läss från partitionen, de läss i ordning.

Med den här konfigurationen bör du tänka på att om den partition som du skickar till inte är tillgänglig får du ett felmeddelande. Som en jämförelsepunkt, om du inte har en tillhörighet till en enda partition, skickar eventhubbar-tjänsten händelsen till nästa tillgängliga partition.

En möjlig lösning för att säkerställa beställning, samtidigt som du maximerar tiden, skulle vara att samla händelser som en del av ditt händelsebearbetningsprogram. Det enklaste sättet att åstadkomma detta är att stämpla din händelse med en anpassad sekvensnummeregenskap. Följande kod visar ett exempel:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 eller senare)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 eller tidigare)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Det här exemplet skickar händelsen till en av de tillgängliga partitionerna i händelsehubben och anger motsvarande sekvensnummer från ditt program. Den här lösningen kräver tillstånd som ska behållas av ditt bearbetningsprogram, men ger dina avsändare en slutpunkt som är mer sannolikt att vara tillgänglig.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över tjänsten Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
