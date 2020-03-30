---
title: .NET-programmeringsguide – Azure Event Hubs (äldre) | Microsoft-dokument
description: Den här artikeln innehåller information om hur du skriver kod för Azure Event Hubs med Hjälp av Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280981"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET-programmeringsguide för Azure Event Hubs (äldre Microsoft.Azure.EventHubs-paket)
I den här artikeln beskrivs några vanliga scenarier med att skriva kod med Hjälp av Azure Event Hubs. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Den här guiden är för det gamla **Microsoft.Azure.EventHubs-paketet.** Vi rekommenderar att du [migrerar](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) koden för att använda det senaste [Azure.Messaging.EventHubs-paketet.](get-started-dotnet-standard-send-v2.md)  


## <a name="event-publishers"></a>Händelseutfärdare

Du skickar händelser till en händelsehubb som antingen använder HTTP POST eller via en AMQP 1.0-anslutning. Valet av vilken du vill använda och när beror på vilket scenario som behandlas. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient][] och [EventData][]. [EventHubClient][] tillhandahåller amqp-kommunikationskanalen över vilken händelser som skickas till händelsehubben. Klassen [EventData][] representerar en händelse och används för att publicera meddelanden till en händelsehubb. Den här klassen innehåller brödtexten, vissa metadata(egenskaper) och rubrikinformation (SystemProperties) om händelsen. Andra egenskaper läggs till i [EventData-objektet][] när det passerar genom en händelsehubb.

## <a name="get-started"></a>Komma igång
.NET-klasserna som stöder eventhubbar finns i [Paketet Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet. Du kan installera med Utforskaren för Visual Studio-lösningen eller [Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan använda Azure-portalen, Azure PowerShell eller Azure CLI för att skapa eventhubbar. Mer information finns i [Skapa ett namnområde för händelsehubbar och en händelsenav med Azure-portalen](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Den primära klassen för interaktion med eventhubbar är [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Du kan instansiera den här klassen med metoden [CreateFromConnectionString,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) som visas i följande exempel:

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

Du skickar händelser till en händelsehubb genom att skapa en [EventHubClient-instans][] och skicka den asynkront via [metoden SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) Den här metoden [EventData][] tar en enda EventData-instansparameter och skickar den asynkront till en händelsehubb.

## <a name="event-serialization"></a>Händelseserialisering

Klassen [EventData][] har [två överbelastade konstruktorer](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) som tar en mängd olika parametrar, byte eller en byte-matris, som representerar händelsedatanyttolasten. När du använder JSON med [EventData][] kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng. Ett exempel:

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
> Om du inte är bekant med partitioner läser du [den här artikeln](event-hubs-features.md#partitions). 

När du skickar händelsedata kan du ange ett värde som hasheras för att skapa en partitionstilldelning. Du anger partitionen med [egenskapen PartitionSender.PartitionID.](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) Beslutet att använda partitioner innebär dock ett val mellan tillgänglighet och konsekvens. 

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Det är valfritt att använda en partitionsnyckel och du bör noga överväga om du ska använda en eller inte. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen. I många fall är det ett bra val att använda en partitionsnyckel om händelsebeställning är viktigt. När du använder en partitionsnyckel kräver dessa partitioner tillgänglighet på en enda nod och avbrott kan inträffa med tiden. till exempel när beräkningsnoder startas om och korrigeringsfilen. Om du anger ett partitions-ID och den partitionen blir otillgänglig av någon anledning misslyckas ett försök att komma åt data i den partitionen. Om hög tillgänglighet är viktigast ska du inte ange någon partitionsnyckel. I så fall skickas händelser till partitioner med hjälp av den avrundningsmodell som beskrivits tidigare. I det här fallet gör du ett uttryckligt val mellan tillgänglighet (inget partitions-ID) och konsekvens (fästa händelser på ett partitions-ID).

En annan faktor är att hantera förseningar i behandlingen av händelser. I vissa fall kan det vara bättre att släppa data och försöka igen än att försöka hålla jämna steg med bearbetningen, vilket kan orsaka ytterligare fördröjningar i senare led. Med en lagerkvalsare är det till exempel bättre att vänta på fullständiga uppdaterade data, men i en livechatt eller VOIP-scenario vill du hellre ha data snabbt, även om de inte är klara.

Med tanke på dessa tillgänglighetsöverväganden kan du i dessa scenarier välja en av följande felhanteringsstrategier:

- Stoppa (sluta läsa från Event Hubs tills saker är fasta)
- Släpp (meddelanden är inte viktiga, släpp dem)
- Försök igen (försök igen meddelandena som du vill)

Mer information och en diskussion om kompromisserna mellan tillgänglighet och konsekvens finns [i Tillgänglighet och konsekvens i händelsehubbar](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser

Att skicka händelser i batchar kan bidra till att öka dataflödet. Du kan använda [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API för att skapa en batch som dataobjekt senare kan läggas till för ett [SendAsync-anrop.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

En enskild batch får inte överskrida gränsen på 1 MB för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten. Du kan använda hjälpmetoden [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) för att säkerställa att batchen inte överstiger 1 MB. Du får en tom [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) från [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API och sedan använda [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) för att lägga till händelser för att konstruera batchen. 

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala

Du skickar händelser till en händelsehubb asynkront. Om du skickar asynkront ökar hastigheten med vilken en klient kan skicka händelser. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) returnerar ett [aktivitetsobjekt.](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) Du kan använda klassen [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) på klienten för att styra alternativen för återförsök för klienter.

## <a name="event-consumers"></a>Händelsekonsumenter
Klassen [EventProcessorHost][] bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost][] ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost][] kan du implementera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Det här gränssnittet innehåller fyra metoder:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Om du vill starta händelsebearbetning instansierar du [EventProcessorHost][]och tillhandahåller lämpliga parametrar för händelsehubben. Ett exempel:

> [!NOTE]
> EventProcessorHost och dess relaterade klasser finns i **paketet Microsoft.Azure.EventHubs.Processor.** Lägg till paketet i Visual Studio-projektet genom att följa instruktionerna i [den här artikeln](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) eller genom att utfärda följande kommando i fönstret Package Manager [Console:](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) `Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Anropa sedan [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) för att registrera implementeringen [av IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) med körningen:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Nu försöker värden skaffa ett lån på varje partition i händelsehubben med hjälp av en "girig" algoritm. Dessa lån varar under en viss tidsram och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom Event Hubs inte har ett direkt koncept för meddelandeantal är genomsnittlig CPU-användning ofta den bästa mekanismen för att mäta back end eller konsumentskala. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost][] implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare

Förutom de avancerade körningsfunktionerna i Event Processor Host möjliggör tjänsten Event Hubs [återkallning](/rest/api/eventhub/revoke-publisher) av utgivare för att blockera specifika utgivare från att skicka händelser till en händelsehubb. Dessa funktioner är användbara om en utgivartoken har komprometterats eller om en programuppdatering gör att de beter sig olämpligt. I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

> [!NOTE]
> För närvarande stöder endast REST API den här funktionen[(återkallning av utgivare).](/rest/api/eventhub/revoke-publisher)

Mer information om att återkalla utgivare och om hur du skickar till Event Hubs som utgivare finns i exemplet [Service Bus Event Hubs säker publicering i stor skala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Event Hubs API-översikt](event-hubs-api-overview.md)
* [Vad är eventhubbar](event-hubs-what-is-event-hubs.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Händelsebearbetningsvärd API-referens](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
