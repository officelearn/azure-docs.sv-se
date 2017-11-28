---
title: "Programmeringsguide för händelsehubbar i Azure | Microsoft Docs"
description: "Skriva kod för Händelsehubbar i Azure med Azure .NET SDK."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/16/2017
ms.author: sethm
ms.openlocfilehash: d6cc4d95adb52b5b0bfc4b674ade878af764a3e7
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="event-hubs-programming-guide"></a>Programmeringsguide för händelsehubbar

Den här artikeln beskrivs några vanliga scenarier i att skriva kod med Azure Event Hubs och Azure .NET SDK. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Händelseutfärdare

Du kan skicka händelser till en händelsehubb antingen med hjälp av HTTP POST eller via en AMQP 1.0-anslutning. Valet av som du vill använda och när beror på det specifika scenario du står inför. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

Du kan skapa och hantera Händelsehubbar med den [NamespaceManager][] klass. När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) och [EventData][]. [EventHubClient][] innehåller kommunikationskanalen AMQP som händelser skickas till händelsehubben. Den [EventData][] klass representerar en händelse och används för att publicera meddelanden i en händelsehubb. Den här klassen innehåller brödtexten, vissa metadata och rubrikinformation om händelsen. Andra egenskaper läggs till i [EventData][] när det överförs via en händelsehubb.

## <a name="get-started"></a>Kom igång

De .NET-klasser som har stöd för händelsehubbar finns i Microsoft.ServiceBus.dll-sammansättningen. Det enklaste sättet att referera till Service Bus-API och att konfigurera ditt program med alla Service Bus-beroenden är att hämta [Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Du kan också använda [Package Manager-konsolen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb
Du kan använda klassen [NamespaceManager][] för att skapa händelsehubbar. Exempel:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

I de flesta fall bör du använda metoderna [CreateEventHubIfNotExists][] för att undvika att generera fel om tjänsten startas om. Exempel:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Alla åtgärder för att skapa händelsehubbar inklusive [CreateEventHubIfNotExists][], kräver **hanterings**-behörigheter för det relevanta namnområdet. Om du vill begränsa behörigheterna för dina utfärdar- eller konsumentprogram kan du undvika de här anropen för att skapa åtgärder i produktionskod när du använder autentiseringsuppgifter med begränsad behörighet.

Den [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) klassen innehåller information om en händelsehubb, inklusive auktoriseringsreglerna, meddelande Kvarhållningsintervall, partitions-ID: N, status och sökväg. Du kan använda den här klassen för att uppdatera metadata i en händelsehubb.

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient
Den primära klassen för att interagera med Händelsehubbar är [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]. Den här klassen innehåller både sändar- och mottagarfunktioner. Du kan skapa en instans av den här klassen med hjälp av den [skapa](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) -metoden, som visas i följande exempel:

```csharp
var client = EventHubClient.Create(description.Path);
```

Den här metoden använder anslutningsinformationen för Service Bus i App.config-filen i `appSettings`-avsnittet. Ett exempel på den `appSettings`-XML som används för att lagra informationen om Service Bus-anslutningen finns i dokumentationen för metoden [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_).

Ett annat alternativ är att skapa klienten från en anslutningssträng. Det här alternativet fungerar bra när du använder arbetsroller i Azure, eftersom du kan lagra strängen i konfigurationsegenskaperna för arbetaren. Exempel:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

Anslutningssträngen blir i samma format som den visas i App.config-filen för föregående metoder:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Slutligen är det också möjligt att skapa en [EventHubClient][] objekt från en [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) instansen, som visas i följande exempel:

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Det är viktigt att Observera att ytterligare [EventHubClient][] objekt som skapas från en instans av en meddelandefabrik återanvänder samma underliggande TCP-anslutningen. Därför har de här objekten en gräns för genomflöde på klientsidan. Metoden [Skapa](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) återanvänder en enda meddelandefabrik. Om du behöver mycket hög genomströmning från en enda avsändare kan du skapa flera meddelandefabriker och ett [EventHubClient][]-objekt från respektive meddelandefabrik.

## <a name="send-events-to-an-event-hub"></a>Skicka händelser till en händelsehubb
Du skickar händelser till en händelsehubb genom att skapa en [EventData][] instansen och skicka den via den [skicka](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) metod. Den här metoden tar en enda [EventData][] parameter i instansen och skickar den synkront till en händelsehubb.

## <a name="event-serialization"></a>Händelseserialisering
Klassen [EventData][] har [fyra överbelastade konstruktorer](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_) som tar olika parametrar, till exempel ett objekt och en serialiserare, en bytematris eller en ström. Det är också möjligt att skapa en instans av klassen [EventData][] och ange brödtextströmmen efteråt. När du använder JSON med [EventData][] kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng.

## <a name="partition-key"></a>Partitionsnyckeln
Klassen [EventData][] har en [PartitionKey][]-egenskap som gör att avsändaren kan ange ett värde som hashas för att skapa en tilldelning av partitionen. En partitionsnyckel garanterar att alla händelser med samma nyckel skickas till samma partition i hubben. Vanliga partitionsnycklar är användarsession-ID:n och unika avsändar-ID:n. Egenskapen [PartitionKey][] är valfri och kan anges när du använder metoderna [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) eller [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_). Om du inte anger ett värde för [PartitionKey][] distribueras skickade händelser till partitioner enligt en resursallokeringsmodell.

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Med hjälp av en partitionsnyckel är valfri och du bör noggrant om du använder en eller inte. I många fall är med en partitionsnyckel ett bra val om händelsen ordning är viktig. När du använder en partitionsnyckel partitionerna kräver tillgänglighet på en enda nod och driftavbrott med tiden. till exempel när compute-noder omstart och korrigering. Om du ställer in ett partitions-ID och den aktuella partitionen är tillgänglig av någon anledning, misslyckas därför ett försök att komma åt data i den aktuella partitionen. Om hög tillgänglighet är de viktigaste inte ange en partitionsnyckel; i så fall skickas händelser till partitioner enligt en resursallokeringsmodell som beskrivs ovan. I detta scenario upprättar du ett uttryckligt val mellan tillgänglighet (ingen partitions-ID) och konsekvens (fästning händelser till en partitions-ID).

Ett annat övervägande hanterar fördröjningar i bearbetning av händelser. I vissa fall kan det vara bättre att släppa data och försök igen än att försöka Håll dig uppdaterad med bearbetning, vilket kan medföra ytterligare bearbetningen nedströms fördröjningar. Till exempel med ett lager ticker är det bättre att vänta för fullständig uppdaterade data, men i en levande chatt eller VOIP-scenario i stället har du data snabbt, även om den inte är klar.

Tänk också på tillgänglighet, i dessa scenarier kan du välja något av följande fel strategier för hantering:

- Stoppa (stop läsning från Händelsehubbar förrän saker har åtgärdats)
- Släpp (meddelandena inte viktig, släppa dem)
- Försök (försök meddelanden som du ser passar)
- [Obeställbara](../service-bus-messaging/service-bus-dead-letter-queues.md) (använda en kö eller en annan händelsehubb till dead letter endast de meddelanden som du gick inte att bearbeta)

Mer information och en beskrivning av hur avvägningarna mellan tillgänglighet och konsekvens finns [tillgänglighet och konsekvens i Händelsehubbar](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser
Att skicka händelser i batchar hjälper dig att öka genomflödet. Den [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) metoden tar en **IEnumerable** parameter av typen [EventData][] och skickar hela batchen som en atomisk åtgärd till händelsehubben.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Observera att en enskild batch inte får överstiga 256 KB-gränsen för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten. Du kan använda hjälpklass [EventHubClient.CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) att säkerställa att batchen inte överskrider 256 KB. Du får en tom [EventDataBatch](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch) från den [CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) API och sedan använda [TryAdd](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.tryadd#Microsoft_ServiceBus_Messaging_EventDataBatch_TryAdd_Microsoft_ServiceBus_Messaging_EventData_) lägga till händelser för att skapa gruppen. Använd slutligen [EventDataBatch.ToEnumerable](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.toenumerable) underliggande händelser ska skickas till den [EventHubClient.Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) API.

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala
Du kan även skicka händelser till en händelsehubb asynkront. Att skicka asynkront kan göra att en klient kan skicka händelser mer frekvent. Både metoderna [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) och [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendbatch) är tillgängliga i asynkrona versioner som returnerar ett [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-objekt. Även om den här tekniken kan öka genomflödet kan den också göra att klienten fortsätter att skicka händelser även när den begränsas av händelsehubbtjänsten. Det kan leda till att klienten råkar ut för fel eller förlorade meddelanden om tekniken inte implementeras på ett korrekt sätt. Du kan dessutom använda egenskapen [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity.retrypolicy) på klienten för att styra alternativ för att försöka igen i klienten.

## <a name="create-a-partition-sender"></a>Skapa en partitionsavsändare
I vissa fall kanske du vill skicka händelser direkt till en given partition, även om det är vanligt att skicka händelser till en händelsehubb utan någon partitionsnyckel. Exempel:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) returnerar ett [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) objekt som du kan använda för att publicera händelser i en viss händelse hubb partition.

## <a name="event-consumers"></a>Händelsekonsumenter
Händelsehubbar har två primära modeller för händelsekonsumtion: direkta mottagare och abstraktioner på högre nivåer som t.ex. [EventProcessorHost][]. Direkta mottagare är ansvariga för sin egen samordning av åtkomst till partitioner inom en *konsumentgrupp*. En konsumentgrupp är en vy (tillstånd, position eller offset) i en partitionerad händelsehubb.

### <a name="direct-consumer"></a>Direktkonsumenten
Det snabbaste sättet att läsa från en partition är att använda den [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) klass. Om du vill skapa en instans av den här klassen måste du använda en instans av klassen [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup). I följande exempel måste partitions-ID anges när du skapar mottagaren för konsumentgruppen:

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Metoden [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) har flera överlagringar som  underlättar kontroll över den läsare som skapas. De här metoderna omfattar att ange en offset antingen som en sträng eller en tidsstämpel, och möjligheten att ange om du ska ta med den här angivna offseten i den returnerade strömmen eller starta efter den. När du har skapat mottagaren kan du börja ta emot händelser på det returnerade objektet. Metoden [Receive](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) har fyra överlagringar som styr parametrarna för mottagningsåtgärden, t.ex. batchstorlek och väntetid. Du kan använda de asynkrona versionerna av de här metoderna för att öka genomflödet av en konsument. Exempel:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Med avseende på en specifik partition tas emot meddelandena i den ordning som de skickades till händelsehubben. Offseten är en strängtoken som används för att identifiera ett meddelande i en partition.

Observera att en enskild partition inte kan ha mer än 5 läsare anslutna när som helst. När läsare ansluts eller kopplas från, kan sessionerna förbli aktiva i flera minuter innan tjänsten känner av att de har kopplats från. Under den här tiden kan det vara omöjligt att återansluta till en partition. En komplett exempel för att skriva en direkt mottagare för Händelsehubbar finns i [Event Hubs Direct Receivers](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) exempel.

### <a name="event-processor-host"></a>Värd för händelsebearbetning
Klassen [EventProcessorHost][] bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost][] ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost][] kan du implementera [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor). Det här gränssnittet innehåller tre metoder:

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Om du vill starta händelsebearbetning instansiera [EventProcessorHost][], ger lämpliga parametrar för händelsehubben. Anropa sedan [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) för att registrera din implementering av [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) i körningen. Värden försöker nu att skaffa en leasing för varje partition i händelsehubben med hjälp av en ”girig” algoritm. De här leasingarna varar en viss tid och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom Händelsehubbar inte har ett direkt begrepp om antalet meddelanden, Genomsnittlig CPU-användning är ofta den bästa metoden för att mäta tillbaka skalan för serverdelen eller konsumenterna. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost][] implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare
Förutom de avancerade funktionerna för körning av [EventProcessorHost][], Event Hubs gör att återkalla utgivare för att hindra specifika utgivare från att skicka händelser till en händelsehubb. Dessa funktioner är användbart om en utgivartoken har komprometterats eller om en programuppdatering får dem att bete sig felaktigt. I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

Mer information om att återkalla utgivare och om hur du skickar till Event Hubs som utgivare finns i exemplet [Service Bus Event Hubs säker publicering i stor skala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Nästa steg
Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Event Hubs API-översikt](event-hubs-api-overview.md)
* [Vad är Händelsehubbar](event-hubs-what-is-event-hubs.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Händelsebearbetningsvärd API-referens](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
