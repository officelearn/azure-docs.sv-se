---
title: 'Översikt över Azure Event Hubs .NET Standard API: er | Microsoft Docs'
description: Översikt över .NET standard-API
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: f8cd167d8de738674939180e0d81fc9282434155
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450463"
---
# <a name="event-hubs-net-standard-api-overview"></a>Översikt över Event Hubs .NET Standard-API

Den här artikeln sammanfattas några av nyckeln för Azure Event Hubs [.NET Standard klientens API: er](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Det finns för närvarande två .NET Standard-klientbibliotek för Event Hubs:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Innehåller alla basic runtime-åtgärder.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Lägger till ytterligare funktioner som gör det möjligt att hålla reda på bearbetade händelser och är det enklaste sättet att läsa från en event hub.

## <a name="event-hubs-client"></a>Event Hubs-klienten

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) är det primära objekt som du använder för att skicka händelser, skapa mottagare och för att hämta information om körning. Den här klienten är länkad till en viss event hub och skapar en ny anslutning till den Event Hubs-slutpunkten.

### <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

En [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objektet har skapats från en anslutningssträng. Det enklaste sättet att skapa en instans av en ny klient visas i följande exempel:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Om du vill redigera programmässigt anslutningssträngen, du kan använda den [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) klassen och skicka anslutningssträngen som en parameter till [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Skicka händelser

Använda för att skicka händelser till en händelsehubb, den [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) klass. Innehållet måste vara en `byte` matris, eller en `byte` matris segment.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Ta emot händelser

Det rekommenderade sättet att ta emot händelser från Event Hubs använder den [Eventprocessorhost](#event-processor-host-apis), som ger funktioner för att automatiskt att hålla reda på förskjutning och partition-information för hubben. Det finns dock vissa situationer där du vilja använda flexibiliteten i Event Hubs-Kärnbibliotek för att ta emot händelser.

#### <a name="create-a-receiver"></a>Skapa en mottagare

Mottagare är knutna till specifika partitioner, så för att kunna ta emot alla händelser i en händelsehubb måste du skapa flera instanser. Det är en bra idé att ha partitionsinformationen programmässigt, i stället för att hårdkoda partitions-ID: N. Du kan använda för att göra det i [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) metod.

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

Du måste ange rätt startpunkt eftersom händelser tas aldrig bort från en event hub (och endast upphör att gälla). I följande exempel visas möjliga kombinationer:

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

## <a name="event-processor-host-apis"></a>Event Processor Host API: er

Dessa API: er ger återhämtning till arbetsprocesser som kan bli tillgänglig, genom att distribuera partitioner över tillgängliga arbeten:

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

Följande är ett exempel på implementering av den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) gränssnitt:

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
* [Tillgängliga Event Hubs API: er](event-hubs-api-overview.md)

.NET API-referenser finns här:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)