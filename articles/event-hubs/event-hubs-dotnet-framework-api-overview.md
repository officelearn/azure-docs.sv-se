---
title: 'Översikt över Azure Event Hubs API: er för .NET Framework | Microsoft Docs'
description: 'En sammanfattning av några av de viktiga Event Hubs .NET Framework-klienten API: er.'
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 46b0599e6c4d5b4cc2dd9370a28616019b79b50d
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745659"
---
# <a name="event-hubs-net-framework-api-overview"></a>Översikt över Event Hubs – .NET Framework API

Den här artikeln sammanfattas några av nyckeln för Azure Event Hubs [.NET Framework-klientens API: er](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Det finns två kategorier: management och API: er för körning. Körning API: er består av alla åtgärder som behövs för att skicka och ta emot ett meddelande. Management-åtgärder kan du hantera ett Event Hubs-enhetstillstånd genom att skapa, uppdatera och ta bort entiteter.

[Övervakningsscenarier](event-hubs-metrics-azure-monitor.md) omfattar både hantering och körning. Detaljerad dokumentation om .NET-API: er, finns det [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs), och [EventProcessorHost API](/dotnet/api/microsoft.azure.eventhubs.processor) referenser.

## <a name="management-apis"></a>Management-API: er

Om du vill utföra följande hanteringsåtgärder för måste du ha **hantera** behörigheter för Event Hubs-namnområde:

### <a name="create"></a>Skapa

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Uppdatera

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Ta bort

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>API: er för körning
### <a name="create-publisher"></a>Skapa publikation

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Publicera meddelande

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Skapa konsument

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Använda meddelande

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Event Processor Host API: er

Dessa API: er ger återhämtning till arbetsprocesser som kan bli tillgänglig, genom att distribuera partitioner över tillgängliga arbeten.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

Den [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) gränssnitt definieras enligt följande:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Vad är Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)

.NET API-referenser finns här:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
