---
title: Översikt över Azure Event Hubs .NET Standard API:er | Microsoft-dokument
description: Översikt över .NET Standard API
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821913"
---
# <a name="event-hubs-net-standard-api-overview"></a>Översikt över eventhubbar .NET Standard API

Den här artikeln sammanfattar några av de viktigaste Azure Event Hubs [.NET Standard-klient-API:erna](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Det finns för närvarande två .NET Standard-klientbibliotek för eventhubbar:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Innehåller alla grundläggande körningsåtgärder.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Lägger till ytterligare funktioner som gör det möjligt att hålla reda på bearbetade händelser och är det enklaste sättet att läsa från en händelsehubb.

## <a name="event-hubs-client"></a>Klient för eventhubbar

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) är det primära objekt som du använder för att skicka händelser, skapa mottagare och hämta körningsinformation. Den här klienten är länkad till en viss händelsehubb och skapar en ny anslutning till slutpunkten Event Hubs.

### <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Ett [EventHubClient-objekt](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) skapas från en anslutningssträng. Det enklaste sättet att instansiera en ny klient visas i följande exempel:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Om du vill redigera anslutningssträngen programmässigt kan du använda klassen [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) och skicka anslutningssträngen som en parameter till [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Skicka händelser

Om du vill skicka händelser till en händelsehubb använder du klassen [EventData.](/dotnet/api/microsoft.azure.eventhubs.eventdata) Brödtexten måste `byte` vara en `byte` matris eller ett matrissegment.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Ta emot händelser

Det rekommenderade sättet att ta emot händelser från eventhubbar är att använda [händelseprocessorvärden](#event-processor-host-apis), som tillhandahåller funktioner för att automatiskt hålla reda på information om förskjutning och partition för händelsehubben. Det finns dock vissa situationer där du kanske vill använda flexibiliteten i biblioteket för händelsehubbar för att ta emot händelser.

#### <a name="create-a-receiver"></a>Skapa en mottagare

Mottagare är knutna till specifika partitioner, så för att ta emot alla händelser i en händelsehubb måste du skapa flera instanser. Det är en bra idé att få partitionsinformationen programmässigt, snarare än hårdkodning av partitions-ID: er. För att göra det kan du använda metoden [GetRuntimeInformationAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Eftersom händelser aldrig tas bort från en händelsenav (och endast upphör att gälla) måste du ange rätt startpunkt. I följande exempel visas möjliga kombinationer:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Använda en händelse

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>API:er för värdbehörig för händelseprocessor

Dessa API:er ger återhämtning till arbetsprocesser som kan bli otillgängliga genom att distribuera partitioner mellan tillgängliga arbetare:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Följande är ett exempel på implementering av [Gränssnittet IEventProcessor:](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Vad är Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Api:er för tillgängliga händelsehubbar](event-hubs-api-overview.md)

.NET API-referenser finns här:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
