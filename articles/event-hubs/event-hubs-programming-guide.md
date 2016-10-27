<properties 
    pageTitle="Programmeringsguide för händelsehubbar i Azure | Microsoft Azure"
    description="Beskriver programmering med händelsehubbar i Azure med Azure .NET SDK."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sethm" />


# <a name="event-hubs-programming-guide"></a>Programmeringsguide för händelsehubbar

Den här artikeln beskriver programmering med händelsehubbar i Azure med Azure .NET SDK. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](event-hubs-overview.md).

## <a name="event-publishers"></a>Händelseutfärdare

Man skickar händelser till en händelsehubb antingen med hjälp av HTTP POST eller via en AMQP 1.0-anslutning. Valet av vilken du ska använda beror på det specifika scenario du står inför. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

Du skapar och hanterar händelsehubbar med hjälp av klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) och [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) innehåller kommunikationskanalen AMQP som används för att skicka händelser till händelsehubben. Klassen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) representerar en händelse och används för att publicera meddelanden i en händelsehubb. Den här klassen innehåller brödtexten, vissa metadata och rubrikinformation om händelsen. Andra egenskaper läggs till i objektet [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) när det överförs via en händelsehubb.

## <a name="get-started"></a>Kom igång

De .NET-klasser som har stöd för händelsehubbar finns i Microsoft.ServiceBus.dll-sammansättningen. Det enklaste sättet att referera till Service Bus-API och att konfigurera ditt program med alla Service Bus-beroenden är att hämta [Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Du kan också använda [Package Manager-konsolen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan använda klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) för att skapa händelsehubbar. Exempel:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

I de flesta fall bör du använda metoderna [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) för att undvika att generera fel om tjänsten startas om. Exempel:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Alla åtgärder för att skapa händelsehubbar inklusive [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), kräver **hanterings**-behörigheter för det relevanta namnområdet. Om du vill begränsa behörigheterna för dina utfärdar- eller konsumentprogram kan du undvika de här anropen för att skapa åtgärder i produktionskod när du använder autentiseringsuppgifter med begränsad behörighet.

Klassen [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) innehåller information om en händelsehubb, inklusive auktoriseringsreglerna, kvarhållningsintervallet för meddelanden, partitions-ID:n, status och sökvägar. Du kan använda den här klassen för att uppdatera metadata i en händelsehubb.

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Den primära klassen för att interagera med händelsehubbar är [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Den här klassen innehåller både sändar- och mottagarfunktioner. Du kan skapa en instans av den här klassen med hjälp av metoden [Skapa](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) som visas i följande exempel.

```
var client = EventHubClient.Create(description.Path);
```

Den här metoden använder anslutningsinformationen för Service Bus i App.config-filen i `appSettings`-avsnittet. Ett exempel på den `appSettings`-XML som används för att lagra informationen om Service Bus-anslutningen finns i dokumentationen för metoden [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx).

Ett annat alternativ är att skapa klienten från en anslutningssträng. Det här alternativet fungerar bra när du använder arbetsroller i Azure, eftersom du kan lagra strängen i konfigurationsegenskaperna för arbetaren. Exempel:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

Anslutningssträngen blir i samma format som den visas i App.config-filen för föregående metoder:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Slutligen är det också möjligt att skapa ett [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-objekt från en instans av [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx), enligt följande exempel.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Observera att ytterligare [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-objekt som har skapats från en instans av en meddelandefabrik återanvänder samma underliggande TCP-anslutning. Därför har de här objekten en gräns för genomflöde på klientsidan. Metoden [Skapa](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) återanvänder en enda meddelandefabrik. Om du behöver mycket hög genomströmning från en enda avsändare kan du skapa flera meddelandefabriker och ett [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-objekt från respektive meddelandefabrik.

## <a name="send-events-to-an-event-hub"></a>Skicka händelser till en händelsehubb

Du skickar händelser till en händelsehubb genom att skapa en instans av [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) och skicka den via metoden [Skicka](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx). Den här metoden tar en enda parameter i instansen av [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) och skickar den synkront till en händelsehubb.

## <a name="event-serialization"></a>Händelseserialisering

Klassen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) har [fyra överbelastade konstruktorer](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) som tar olika parametrar, till exempel ett objekt och en serialiserare, en bytematris eller en ström. Det är också möjligt att skapa en instans av klassen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) och ange brödtextströmmen efteråt. När du använder JSON med [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng.

## <a name="partition-key"></a>Partitionsnyckeln

Klassen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) har en [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx)-egenskap som gör att avsändaren kan ange ett värde som hashas för att skapa en tilldelning av partitionen. Användning av en partitionsnyckel garanterar att alla händelser med samma nyckel skickas till samma partition i händelsehubben. Vanliga partitionsnycklar är användarsession-ID:n och unika avsändar-ID:n. Egenskapen [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) är valfri och kan anges när du använder metoderna [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) eller [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Om du inte anger ett värde för [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) distribueras skickade händelser till partitioner enligt en resursallokeringsmodell.

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser

Att skicka händelser i batchar kan öka genomflödet dramatiskt. Metoden [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) tar en **IEnumerable**-parameter av typen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) och skickar hela batchen som en atomisk åtgärd till händelsehubben.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Observera att en enskild batch inte får överstiga 256 KB-gränsen för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten.

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala

Du kan även skicka händelser till en händelsehubb asynkront. Att skicka asynkront kan göra att en klient kan skicka händelser mer frekvent. Både metoderna [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) och [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) är tillgängliga i asynkrona versioner som returnerar ett [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-objekt. Även om den här tekniken kan öka genomflödet kan den också göra att klienten fortsätter att skicka händelser även när den begränsas av händelsehubbtjänsten. Det kan leda till att klienten råkar ut för fel eller förlorade meddelanden om tekniken inte implementeras på ett korrekt sätt. Du kan dessutom använda egenskapen [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) på klienten för att styra alternativ för att försöka igen i klienten.

## <a name="create-a-partition-sender"></a>Skapa en partitionsavsändare

Det vanligaste är att man skickar händelser till en händelsehubb med en partitionsnyckel, men i vissa fall kanske du vill skicka händelser direkt till en viss partition. Exempel:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) returnerar ett [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx)-objekt som du kan använda för att publicera händelser i en specifik partition i händelsehubben.

## <a name="event-consumers"></a>Händelsekonsumenter

Händelsehubbar har två primära modeller för händelsekonsumtion: direkta mottagare och abstraktioner på högre nivåer som t.ex. [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). Direkta mottagare är ansvariga för sin egen samordning av åtkomst till partitioner inom en konsumentgrupp.

### <a name="direct-consumer"></a>Direktkonsumenten

Det snabbaste sättet att läsa från en partition inom en konsumentgrupp är att använda klassen [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx). Om du vill skapa en instans av den här klassen måste du använda en instans av klassen [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). I följande exempel måste partitions-ID:et anges när du skapar mottagaren för konsumentgruppen.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Metoden [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) har flera överlagringar som  underlättar kontroll över den läsare som skapas. De här metoderna omfattar att ange en offset antingen som en sträng eller en tidsstämpel, och möjligheten att ange om du ska ta med den här angivna offseten i den returnerade strömmen eller starta efter den. När du har skapat mottagaren kan du börja ta emot händelser på det returnerade objektet. Metoden [Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) har fyra överlagringar som styr parametrarna för mottagningsåtgärden, t.ex. batchstorlek och väntetid. Du kan använda de asynkrona versionerna av de här metoderna för att öka genomflödet av en konsument. Exempel:

```
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

Vad gäller en specifik partition tas meddelandena emot i den ordning som de skickades till händelsehubben. Offseten är en strängtoken som används för att identifiera ett meddelande i en partition.

Observera att en enda partition inom en konsumentgrupp inte samtidigt kan ha fler än fem läsare anslutna vid en viss tidpunkt. När läsare ansluts eller kopplas från, kan sessionerna förbli aktiva i flera minuter innan tjänsten känner av att de har kopplats från. Under den här tiden kan det vara omöjligt att återansluta till en partition. Ett utförligt exempel på att skriva en direkt mottagare för händelshubbar finns i exemplet [Service Bus Event Hubs Direct Receivers](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### <a name="event-processor-host"></a>Värd för händelsebearbetning

Klassen [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) kan du implementera [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx). Det här gränssnittet innehåller tre metoder:

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Om du vill starta händelsebearbetning skapar du en instans av [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) som ger lämpliga parametrar för händelsehubben. Anropa sedan [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) för att registrera din implementering av [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) i körningen. I det här läget kommer värden att försöka skaffa en leasing för varje partition i händelsehubben med hjälp av en ”girig” algoritm. De här leasingarna varar en viss tid och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom händelsehubbar inte har ett direkt begrepp om antalet meddelanden är den genomsnittliga processoranvändningen oftast den bästa metoden för att mäta skalan för serverdelen eller konsumenterna. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare

Förutom de avancerade körningsfunktionerna i [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) gör händelsehubbar det möjligt att återkalla utgivare och hindra specifika utgivare från att skicka händelser till en händelsehubb. De här funktionerna är särskilt användbara om en utgivartoken har komprometterats eller om en programuppdatering får dem att bete sig på ett olämpligt sätt I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

Mer information om att återkalla utgivare och om hur du skickar till Event Hubs som utgivare finns i exemplet [Service Bus Event Hubs säker publicering i stor skala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

- [Event Hubs API-översikt](event-hubs-api-overview.md)
- [Event Hubs-översikt](event-hubs-overview.md)
- [Event Hubs-kodexempel](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Händelsebearbetningsvärd API-referens](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)



<!--HONumber=Oct16_HO3-->


