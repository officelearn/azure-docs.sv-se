---
title: Programmeringsguide för händelsehubbar i Azure | Microsoft Docs
description: Skriv kod för Azure Event Hubs med Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.topic: article
ms.date: 06/12/2018
ms.author: shvija
ms.openlocfilehash: f2ae9835c412b177a1b80044613a45eb96dfeeb8
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006891"
---
# <a name="event-hubs-programming-guide"></a>Programmeringsguide för händelsehubbar

Den här artikeln beskriver några vanliga scenarier i skriva kod med Azure Event Hubs. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Händelseutfärdare

Du kan skicka händelser till en händelsehubb antingen med hjälp av HTTP POST eller via en AMQP 1.0-anslutning. Valet av som du vill använda och när beror på det specifika scenario du håller på att åtgärdas. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient][] och [EventData][]. [EventHubClient][] innehåller kommunikationskanalen AMQP som händelser skickas till händelsehubben. Den [EventData][] klass representerar en händelse och används för att publicera meddelanden till en händelsehubb. Den här klassen innehåller brödtexten, vissa metadata och rubrikinformation om händelsen. Andra egenskaper läggs till i [EventData][] objekt när den går igenom en event hub.

## <a name="get-started"></a>Kom igång

.NET-klasser som har stöd för Händelsehubbar finns i den [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-paketet. Du kan installera med hjälp av Visual Studio Solution explorer eller [Pakethanterarkonsolen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan använda den Azure-portalen, Azure PowerShell eller Azure CLI för att skapa Händelsehubbar. Mer information finns i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Den primära klassen för att interagera med Händelsehubbar är [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Du kan skapa en instans av den här klassen med hjälp av den [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) metod, som visas i följande exempel:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Skicka händelser till en händelsehubb

Du skicka händelser till en händelsehubb genom att skapa en [EventHubClient][] instans och skicka den asynkront via den [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metod. Den här metoden tar en enda [EventData][] instansparametern och skickar den synkront till en händelsehubb.

## <a name="event-serialization"></a>Händelseserialisering

Den [EventData][] klassen har [två överbelastade konstruktorer](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) som tar olika parametrar, byte eller en bytematris som representerar data händelsenyttolast. När du använder JSON med [EventData][] kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng. Exempel:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitionsnyckeln

När du skickar händelsedata, kan du ange ett värde som hashas för att skapa en tilldelning av partitionen. Du anger en partition med hjälp av den [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) egenskapen. Beslutet att använda partitioner innebär dock ett val mellan tillgänglighet och konsekvens. 

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Det är valfritt att använda en partitionsnyckel och bör du noggrant om du använder en eller inte. I många fall är med en partitionsnyckel ett bra alternativ om Händelseordning är viktigt. När du använder en partitionsnyckel partitionerna kräver tillgänglighet på en enda nod och avbrott kan uppstå med tiden. till exempel när compute-noder omstart och korrigeringsnivå. Om du ställer in ett partitions-ID och den partitionen som är tillgänglig av någon anledning, misslyckas därför försöker komma åt data i partitionen. Om hög tillgänglighet är den viktigaste inte ange en partitionsnyckel; i så fall skickas händelser till partitioner som använder resursallokeringsmodell som beskrivs ovan. I det här scenariot gör du ett explicit val mellan tillgänglighet (inga partitions-ID) och konsekvens (fästa händelser till en partitions-ID).

Ett annat övervägande hantering av fördröjningar i bearbetning av händelser. I vissa fall kan det vara bättre att släppa data och försök igen än försöker att hålla jämna steg med bearbetning, vilket kan medföra ytterligare fördröjningar i bearbetningen nedströms. Till exempel med en aktiekurser är det bättre att vänta för kompletta uppdaterade data, men i en live-chatt eller VOIP-scenario som du vill hellre ha data snabbt, även om det inte är klar.

Tänk också på tillgänglighet, i dessa scenarier kan du välja något av följande fel strategier för hantering av:

- Stoppa (stop läsa från Event Hubs förrän saker har åtgärdats)
- Ta bort (meddelanden är inte viktigt, släppa dem)
- Försök igen (återförsök meddelanden efter behov)

Mer information och en diskussion om avvägningar mellan tillgänglighet och konsekvens finns i [tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser

Skicka händelser i batchar kan öka genomflödet. Du kan använda den [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API för att skapa en batch som objekt senare kan läggas till för en [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) anropa.

En enskild batch får inte överstiga 256 KB-gränsen för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten. Du kan använda hjälpmetoden [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) att se till att batchen inte överskrider 256 KB. Du får en tom [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) från den [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API och sedan använda [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) att lägga till händelser för att konstruera batchen. 

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala

Du skicka händelser till en händelsehubb asynkront. Skicka asynkront ökar den hastighet som en klient kan skicka händelser. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) returnerar en [uppgift](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objekt. Du kan använda den [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) klass på klienten för att styra klienten antal försök.

## <a name="event-consumers"></a>Händelsekonsumenter

Klassen [EventProcessorHost][] bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost][] ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost][] kan du implementera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Det här gränssnittet innehåller fyra metoder:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Om du vill starta händelsebearbetning instansiera [EventProcessorHost][], ger lämpliga parametrar för din händelsehubb. Exempel:

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Anropa sedan [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) att registrera din [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementering med runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Värden försöker nu att skaffa en leasing för varje partition i händelsehubben med hjälp av en ”girig” algoritm. De här leasingarna varar en viss tid och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom Händelsehubbar inte har ett direkt begrepp om antalet meddelanden Genomsnittlig CPU-användning är ofta den bästa metoden för att mäta tillbaka eller konsumenterna skala. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost][] implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare

Förutom de avancerade funktionerna för körning av [EventProcessorHost][], Event Hubs möjligt att återkalla utgivare för att hindra specifika utgivare från att skicka händelser till en händelsehubb. Dessa funktioner är användbara om en utgivartoken har komprometterats, eller en programuppdatering får dem att bete sig på ett olämpligt sätt. I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

Mer information om att återkalla utgivare och om hur du skickar till Event Hubs som utgivare finns i exemplet [Service Bus Event Hubs säker publicering i stor skala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Event Hubs API-översikt](event-hubs-api-overview.md)
* [Vad är Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Händelsebearbetningsvärd API-referens](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
