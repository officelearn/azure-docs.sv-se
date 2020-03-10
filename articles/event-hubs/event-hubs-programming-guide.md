---
title: Programmerings guide för .NET – Azure Event Hubs (bakåtkompatibelt) | Microsoft Docs
description: Den här artikeln innehåller information om hur du skriva kod för Azure Event Hubs med med Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945612"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Programmerings guide för .NET för Azure Event Hubs (äldre Microsoft. Azure. EventHubs-paket)
Den här artikeln beskriver några vanliga scenarier i skriva kod med Azure Event Hubs. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Den här guiden gäller för det gamla **Microsoft. Azure. EventHubs** -paketet. Vi rekommenderar att du [migrerar](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) koden så att den använder det senaste [Azure. Messaging. EventHubs](get-started-dotnet-standard-send-v2.md) -paketet.  


## <a name="event-publishers"></a>Händelseutfärdare

Du kan skicka händelser till en händelsehubb antingen med hjälp av HTTP POST eller via en AMQP 1.0-anslutning. Valet av som du vill använda och när beror på det specifika scenario du håller på att åtgärdas. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient][] och [EventData][]. [EventHubClient][] tillhandahåller den AMQP kommunikations kanal som händelser skickas till Event Hub. [EventData][] -klassen representerar en händelse och används för att publicera meddelanden till en Event Hub. Den här klassen innehåller bröd texten, vissa metadata (egenskaper) och rubrik information (SystemProperties) om händelsen. Andra egenskaper läggs till i [EventData][] -objektet när det passerar en händelsehubben.

## <a name="get-started"></a>Kom igång
De .NET-klasser som stöder Event Hubs finns i [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-paketet. Du kan installera med hjälp av Visual Studio Solution Explorer eller [Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan använda den Azure-portalen, Azure PowerShell eller Azure CLI för att skapa Händelsehubbar. Mer information finns i [skapa ett Event Hubs-namnområde och en händelsehubben med hjälp av Azure Portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Den primära klassen för att interagera med Event Hubs är [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Du kan skapa en instans av den här klassen med metoden [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) , som visas i följande exempel:

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

Du skickar händelser till en Event Hub genom att skapa en [EventHubClient][] -instans och skicka den asynkront via [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -metoden. Den här metoden tar en enskild [EventData][] -instans parameter och skickar asynkront den till en Event Hub.

## <a name="event-serialization"></a>Händelseserialisering

Klassen [EventData][] har [två överbelastade konstruktorer](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) som tar en rad olika parametrar, byte eller en byte mat ris som representerar händelse data nytto lasten. När du använder JSON med [EventData][] kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng. Exempel:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitionsnyckeln

> [!NOTE]
> Om du inte är bekant med partitioner kan du läsa [den här artikeln](event-hubs-features.md#partitions). 

När du skickar händelsedata, kan du ange ett värde som hashas för att skapa en tilldelning av partitionen. Du anger partitionen med egenskapen [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . Beslutet att använda partitioner innebär dock ett val mellan tillgänglighet och konsekvens. 

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Det är valfritt att använda en partitionsnyckel och bör du noggrant om du använder en eller inte. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen. I många fall är med en partitionsnyckel ett bra alternativ om Händelseordning är viktigt. När du använder en partitionsnyckel partitionerna kräver tillgänglighet på en enda nod och avbrott kan uppstå med tiden. till exempel när compute-noder omstart och korrigeringsnivå. Om du ställer in ett partitions-ID och den partitionen som är tillgänglig av någon anledning, misslyckas därför försöker komma åt data i partitionen. Om hög tillgänglighet är den viktigaste inte ange en partitionsnyckel; i så fall skickas händelser till partitioner som använder resursallokeringsmodell som beskrivs ovan. I det här scenariot gör du ett explicit val mellan tillgänglighet (inga partitions-ID) och konsekvens (fästa händelser till en partitions-ID).

Ett annat övervägande hantering av fördröjningar i bearbetning av händelser. I vissa fall kan det vara bättre att släppa data och försök igen än försöker att hålla jämna steg med bearbetning, vilket kan medföra ytterligare fördröjningar i bearbetningen nedströms. Till exempel med en aktiekurser är det bättre att vänta för kompletta uppdaterade data, men i en live-chatt eller VOIP-scenario som du vill hellre ha data snabbt, även om det inte är klar.

Tänk också på tillgänglighet, i dessa scenarier kan du välja något av följande fel strategier för hantering av:

- Stoppa (stop läsa från Event Hubs förrän saker har åtgärdats)
- Ta bort (meddelanden är inte viktigt, släppa dem)
- Försök igen (återförsök meddelanden efter behov)

Mer information och en diskussion om kompromisser mellan tillgänglighet och konsekvens finns [i tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser

Skicka händelser i batchar kan öka genomflödet. Du kan använda [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API: et för att skapa en batch som data objekt kan läggas till senare för ett [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -anrop.

En enskild batch får inte överskrida gränsen på 1 MB för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten. Du kan använda hjälp metoden [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) för att se till att batchen inte överskrider 1 MB. Du får en tom [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) från [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API: et och använder sedan [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) för att lägga till händelser för att skapa batchen. 

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala

Du skicka händelser till en händelsehubb asynkront. Skicka asynkront ökar den hastighet som en klient kan skicka händelser. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) returnerar ett [aktivitets](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objekt. Du kan använda [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) -klassen på klienten för att kontrol lera alternativen för klient återförsök.

## <a name="event-consumers"></a>Händelsekonsumenter
Klassen [EventProcessorHost][] bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost][] ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost][] kan du implementera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Det här gränssnittet innehåller fyra metoder:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Starta händelse bearbetningen genom att instansiera [EventProcessorHost][], med lämpliga parametrar för händelsehubben. Exempel:

> [!NOTE]
> EventProcessorHost och dess relaterade klasser finns i paketet **Microsoft. Azure. EventHubs. processor** . Lägg till paketet i Visual Studio-projektet genom att följa anvisningarna i [den här artikeln](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) eller genom att utfärda följande kommando i fönstret [Package Manager-konsol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) :`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Anropa sedan [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) för att registrera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementeringen med körningen:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Värden försöker nu att skaffa en leasing för varje partition i händelsehubben med hjälp av en ”girig” algoritm. De här leasingarna varar en viss tid och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom Händelsehubbar inte har ett direkt begrepp om antalet meddelanden Genomsnittlig CPU-användning är ofta den bästa metoden för att mäta tillbaka eller konsumenterna skala. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost][] implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare

Utöver de avancerade körnings funktionerna i händelse bearbetnings värden, aktiverar Event Hubs tjänsten [återkallning av utgivare](/rest/api/eventhub/revoke-publisher) för att blockera vissa utgivare från att skicka händelser till en Event Hub. Dessa funktioner är användbara om en utgivartoken har komprometterats, eller en programuppdatering får dem att bete sig på ett olämpligt sätt. I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

> [!NOTE]
> För närvarande stöds endast REST API som stöder den här funktionen ([återkallning av utgivare](/rest/api/eventhub/revoke-publisher)).

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
