---
title: Bästa praxis för att förbättra prestanda med hjälp av Azure Service Bus | Microsoft Docs
description: Beskriver hur du använder Service Bus för att optimera prestanda när du skickar asynkrona meddelanden.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: f5ce8a237bc2ba7fe15acfcd6afa0edcda7ef713
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996038"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Metodtips för prestandaförbättringar med hjälp av Service Bus-meddelanden

Den här artikeln beskriver hur du använder Azure Service Bus för att optimera prestanda när du skickar asynkrona meddelanden. Den första delen av den här artikeln beskriver de olika mekanismer som kan användas för att öka prestanda. Den andra delen innehåller råd om hur du använder Service Bus på ett sätt som kan erbjuda den bästa prestandan i ett visst scenario.

I den här artikeln avser termen ”klient” en person som har åtkomst till Service Bus. En klient kan ta rollen som en avsändare eller en mottagare. Termen ”avsändaren” används för en Service Bus-kö eller ämne-klient som skickar meddelanden till en Service Bus-kö eller ämne prenumeration. Termen ”mottagare” refererar till en Service Bus-kö eller prenumeration-klient som tar emot meddelanden från en Service Bus-kö eller prenumeration.

Dessa avsnitt beskrivs flera koncept som Service Bus använder för att öka prestandan.

## <a name="protocols"></a>Protokoll

Service Bus gör att klienter kan skicka och ta emot meddelanden via ett av tre protokoll:

1. Avancerade Message Queuing Protocol (AMQP)
2. Service Bus-meddelandefunktionen Protocol (SBMP)
3. HTTP

AMQP och SBMP är mer effektivt eftersom de upprätthåller anslutningen till Service Bus så länge meddelandefabrik finns. Den implementerar också batchbearbetning och förhämtar. Om du inte uttryckligen nämndes allt innehåll i den här artikeln förutsätter vi att användningen av AMQP- eller SBMP.

## <a name="reusing-factories-and-clients"></a>Återanvända fabriker och klienter

Service Bus-klienten objekt, till exempel [QueueClient] [ QueueClient] eller [MessageSender][MessageSender], skapas via en [ MessagingFactory] [ MessagingFactory] -objektet, vilket också ger interna hantering av anslutningar. Vi rekommenderar att du inte stänger meddelanden fabriker eller kön, ämnet och prenumerationen klienter när du skickar ett meddelande och sedan återskapa dem när du skickar nästa meddelande. Stänga en meddelandefabrik tar bort anslutningen till Service Bus-tjänsten och en ny anslutning upprättas när återskapa fabriken. Upprätta en anslutning är en kostsam åtgärd som du kan undvika genom att återanvända samma factory och klientobjekt för flera åtgärder. Du kan på ett säkert sätt att använda dessa klientobjekt för samtidiga asynkrona åtgärder och från flera trådar. 

## <a name="concurrent-operations"></a>Samtidiga åtgärder

När en åtgärd (skicka, ta emot, ta bort osv.) tar lite tid. Den här gången innefattar bearbetning av åtgärden av Service Bus-tjänsten utöver svarstiden för begäran och svaret. Om du vill öka antalet åtgärder per tid måste operations köras samtidigt. 

Klienten schemalägger samtidiga åtgärder genom att utföra asynkrona åtgärder. Nästa förfrågan har startats innan den förra begäran har slutförts. Följande kodavsnitt är ett exempel på en asynkron sändningsåtgärden:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Följande kod är ett exempel på en asynkron mottagningsåtgärd. Se fullständig programmet [här](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Ta emot läge

När du skapar en kö eller prenumeration klient kan ange du en receive-läge: *Peek-lock* eller *ta emot och ta bort*. Standard mottagningsläge är [PeekLock][PeekLock]. Vid användning i det här läget, skickar klienten en begäran att ta emot ett meddelande från Service Bus. När klienten har tagit emot meddelandet, skickar en begäran om att komplettera meddelandet.

När du ställer in receive-läge [ReceiveAndDelete][ReceiveAndDelete], båda stegen kombineras i en enskild begäran. De här stegen minska det totala antalet åtgärder och kan förbättra det övergripande meddelandedataflödet. Den här prestandaökningen kommer dess risk att förlora meddelanden.

Service Bus stöder inte transaktioner för ta emot och delete-åtgärder. Dessutom peek-lock-semantik som krävs för alla scenarier där klienten vill skjuta upp eller [förlorade](service-bus-dead-letter-queues.md) ett meddelande.

## <a name="client-side-batching"></a>Klientsidan batchbearbetning

Klientsidan batchbearbetning kan en kö eller ämne klient om du vill skicka ett meddelande för en viss tidsperiod. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. Klientsidan batchbearbetning gör att en kö eller prenumeration klient för batch-flera **Slutför** begäranden till en enskild begäran. Batchbearbetning är endast tillgänglig för asynkron **skicka** och **Slutför** åtgärder. Synkrona åtgärder skickas direkt till Service Bus-tjänsten. Batchbearbetning inte inträffa för peek eller ta emot och inte heller batchbearbetning sker till klienter.

Som standard använder en klient ett batch-intervall på 20 ms. Du kan ändra batch-intervall genom att ange den [BatchFlushInterval] [ BatchFlushInterval] egenskapen innan du skapar meddelandefabrik. Den här inställningen påverkar alla klienter som skapats med den här fabriken.

Om du vill inaktivera batchbearbetning, ange den [BatchFlushInterval] [ BatchFlushInterval] egenskap **TimeSpan.Zero**. Exempel:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Batchbearbetning påverkar inte antalet faktureringsbara meddelanden åtgärder och är endast tillgänglig för Service Bus klienten protokoll använder den [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteket. HTTP-protokollet har inte stöd för batchbearbetning.

> [!NOTE]
> Ange BatchFlushInterval garanterar att den batchbearbetning är implicit från programmets perspektiv. dvs. programmet gör SendAsync() och CompleteAsync() anropar och inte göra specifika Batch-anrop.
>
> Explicit klienten sida batchbearbetning kan implementeras genom att använda den nedan metodanrop - 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Här måste den kombinerade storleken av meddelanden vara mindre än den maximala storleken som stöds av prisnivå.

## <a name="batching-store-access"></a>Batchbearbetning store-åtkomst

För att öka dataflödet för en kö, ett ämne eller en prenumeration, slår Service Bus ihop flera meddelanden vid skrivning till arkivet internt. Om aktiverad på en kö eller ämne, ska det kunna batchbearbetas att skriva meddelanden till arkivet. Om aktiverad på en kö eller prenumeration, kommer det kunna batchbearbetas att ta bort meddelanden från store. Om batch store-åtkomst är aktiverat för en entitet, försenar Service Bus store skrivning om entiteten med upp till 20 ms. 

> [!NOTE]
> Det finns ingen risk att förlora meddelanden med batchbearbetning, även om det finns ett Service Bus-fel i slutet av ett batchbearbetningen 20 MS-intervall. 

Ytterligare store-åtgärder som inträffar under det här intervallet har lagts till i gruppen. Batchar store åtkomst endast påverkar **skicka** och **Slutför** operations; ta emot påverkas inte. Satsvis store-åtkomst är en egenskap för en entitet. Batchbearbetning sker över alla entiteter som möjliggör åtkomst till batch store.

När du skapar en ny kö, ett ämne eller en prenumeration, är gruppbaserade store-åtkomst aktiverat som standard. Om du vill inaktivera gruppbaserade store-åtkomst, ange den [EnableBatchedOperations] [ EnableBatchedOperations] egenskap **FALSKT** innan du skapar entiteten. Exempel:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Åtkomst till batch store påverkar inte antalet faktureringsbara meddelanden åtgärder och är en egenskap för en kö, ett ämne eller en prenumeration. Det är oberoende av receive-läge och protokoll som används mellan en klient och Service Bus-tjänsten.

## <a name="prefetching"></a>Förhämtar

[Förhämtar,](service-bus-prefetch.md) gör att kö eller prenumeration klienten att läsa in ytterligare meddelanden från tjänsten när den skickar en receive. Klienten lagrar dessa meddelanden i en lokal cache. Storleken på cacheminnet bestäms av den [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] eller [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] egenskaper. Varje klient som möjliggör förhämtar, upprätthåller sin egen cache. En cache delas inte mellan klienter. Om klienten skickar en receive-åtgärd och dess cachen är tom, överför en grupp med meddelanden i tjänsten. Batchens storlek är lika med storleken på cacheminnet eller 256 KB, beroende på vilket som är mindre. Om klienten skickar en receive-åtgärd och cachen innehåller ett meddelande, tas meddelandet från cachen.

När ett meddelande är prefetched låser tjänsten prefetched meddelandet. Med lås, det går inte att prefetched meddelandet tas emot av en annan mottagare. Om mottagaren inte kan slutföra meddelandet innan låset upphör att gälla, blir meddelandet tillgängligt för andra mottagare. Prefetched kopia av meddelandet behålls på cacheplatsen. Mottagaren som använder den cachelagrade kopian har upphört att gälla får ett undantag vid försök att slutföra meddelandet. Som standard meddelandelåset går ut efter 60 sekunder. Det här värdet kan utökas till 5 minuter. För att förhindra användningen av utgångna meddelanden ska cachestorleken alltid vara mindre än antalet meddelanden som kan användas av en klient i lås-timeout-intervall.

När du använder upphör 60 sekunder som standard för lås är en bra värde för [PrefetchCount] [ SubscriptionClient.PrefetchCount] är 20 gånger maximalt hastighetsprestanda för alla mottagare av fabriken. Till exempel en fabrik skapar tre olika mottagare, och varje mottagaren kan bearbeta upp till 10 meddelanden per sekund. Antalet prefetch får inte överstiga 20 X 3 X 10 = 600. Som standard [PrefetchCount] [ QueueClient.PrefetchCount] anges till 0, vilket innebär att inga ytterligare meddelanden hämtas från tjänsten.

Förhämtar, meddelanden ökar det totala arbetsflödet för en kö eller prenumeration eftersom det minskar det totala antalet meddelandeåtgärder eller tur och RETUR. Hämtar det första meddelandet, men tar längre tid (på grund av ökad meddelandestorleken). Ta emot prefetched meddelanden kommer att snabbare eftersom dessa meddelanden har redan laddats ned av klienten.

Egenskapen time to live (TTL) för ett meddelande som kontrolleras av servern när servern skickar meddelandet till klienten. Klienten kontrollerar inte meddelandets TTL-egenskap när meddelandet tas emot. I stället tas meddelandet emot även om meddelandets TTL har passerat när meddelandet har cachelagras av klienten.

Förhämtar, påverkar inte antalet faktureringsbara meddelanden åtgärder och är endast tillgänglig för Service Bus-klientprotokollet. HTTP-protokollet stöder inte förhämtar. Förhämtar, är tillgänglig för både synkron och asynkron ta emot.

## <a name="prefetching-and-receivebatch"></a>Förhämtar, och ReceiveBatch

Begreppet förhämtar, flera meddelanden tillsammans har liknande semantisk struktur för bearbetning av meddelanden i en batch (ReceiveBatch), finns men det några mindre skillnader som måste finnas i åtanke när möjligheterna med dessa tillsammans.

Prefetch är en konfiguration (eller läge) på klienten (QueueClient och SubscriptionClient) och ReceiveBatch är en åtgärd (som har semantik för begäranden och svar).

När du använder dessa tillsammans att tänka på följande fall-

* Prefetch ska vara större än eller lika med antalet meddelanden som du förväntar dig att ta emot från ReceiveBatch.
* Prefetch kan vara upp till n/3 gånger antalet meddelanden som behandlas per sekund, där n är standardlängden för lås.

Det finns några utmaningar med att ha en girig metod, (d.v.s. och antalet prefetch mycket hög), eftersom det innebär att meddelandet är låst till en viss mottagare. Rekommendationen är att försöka ut förhämtning av värden mellan de tröskelvärden som nämns ovan och empiriskt identifiera vad som passar.

## <a name="multiple-queues"></a>Flera köer

Om den förväntade belastningen inte kan hanteras av en enda partitionerad kö eller ett ämne, måste du använda flera meddelandeentiteter. När du använder flera entiteter kan du skapa en dedikerad klient för varje entitet, istället för att använda samma klient för alla entiteter.

## <a name="development-and-testing-features"></a>Utveckling och testning funktioner

Service Bus har en funktion som används för utveckling, vilket **ska aldrig användas i produktion konfigurationer**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

När nya regler eller filter har lagts till i avsnittet kan du använda [TopicDescription.EnableFilteringMessagesBeforePublishing][] att kontrollera att nya filteruttrycket fungerar som förväntat.

## <a name="scenarios"></a>Scenarier

I följande avsnitt beskrivs vanliga scenarier för meddelanden och beskriver de föredragna inställningarna för Service Bus. Priserna för dataflöde är klassificerade som är så litet (mindre än 1 meddelande per sekund), Måttlig (1 meddelande per sekund eller större men mindre än 100 meddelanden per sekund) och hög (100 meddelanden/sekund eller större). Antalet klienter som är klassificerade som små (5 eller färre), Måttlig (fler än 5 men mindre än eller lika med 20), och stora (mer än 20).

### <a name="high-throughput-queue"></a>Högt dataflöde kö

Mål: Maximera dataflödet för en enskild kö. Antalet sändare och mottagare är liten.

* För att öka den totala frekvensen för Skicka i kön, Använd flera meddelandefabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Använda flera meddelandefabriker för att skapa mottagare för att öka den totala frekvensen receive från kön.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Ange batchbearbetningen intervallet till 50 ms att minska antalet överföringar för Service Bus klient-protokollet. Om du använder flera avsändare, öka batchbearbetningen intervallet 100 ms.
* Lämna gruppbaserade store-åtkomst aktiverat. Den här åtkomsten ökar den totala frekvensen med vilken meddelanden kan skrivas i kön.
* Ange antalet prefetch till 20 gånger de maximala hastighetsprestanda för alla mottagare av en fabrik. Det här antalet minskar antalet överföringar för Service Bus klient-protokollet.
* Använd en partitionerad kö för bättre prestanda och tillgänglighet.

### <a name="multiple-high-throughput-queues"></a>Flera köer med stora dataflöden

Mål: Maximera hela dataflödet för flera köer. Dataflödet för en enskild kö är begränsad eller hög.

Använd inställningarna som beskrivs för att maximera dataflödet för en enskild kö för att få maximalt dataflöde över flera köer. Dessutom kan använda olika fabriker för att skapa klienter som skickar eller tar emot från olika köer.

### <a name="low-latency-queue"></a>Låg latens kö

Mål: Minimera svarstiden för slutpunkt till slutpunkt för en kö eller ämne. Antalet sändare och mottagare är liten. Dataflödet för kön är små eller Måttlig.

* Inaktivera klientsidan batchbearbetning. Skickar klienten omedelbart ett meddelande.
* Inaktivera gruppbaserade store-åtkomst. Tjänsten skriver direkt meddelandet till arkivet.
* Om du använder en enskild klient inställd prefetch antal 20 gånger behandlingstakten för mottagaren. Om flera meddelanden anländer till kön på samma gång, överför dem i Service Bus-klientprotokollet på samma gång. När klienten får nästa meddelande, finns meddelandet redan i den lokala cachen. Cachen bör vara liten.
* Om du använder flera klienter, anger du antalet prefetch till 0. Genom att ange antalet får andra klienten det andra meddelandet medan den första klienten fortfarande bearbetar det första meddelandet.
* Använd en partitionerad kö för bättre prestanda och tillgänglighet.

### <a name="queue-with-a-large-number-of-senders"></a>Med ett stort antal avsändare i kö

Mål: Maximera dataflödet för en kö eller ett ämne med ett stort antal avsändare. Varje avsändare skickar meddelanden med en begränsad hastighet. Antal mottagare är liten.

Service Bus ger upp till 1 000 samtidiga anslutningar till en meddelandeentitet (eller 5000 med hjälp av AMQP). Den här gränsen tillämpas på namnområdesnivå och köer/ämnen/prenumerationer begränsas av gräns för samtidiga anslutningar per namnområde. För köer delas numret mellan avsändarna och mottagarna. Om alla 1000 anslutningar krävs för avsändare, Ersätt kön med ett ämne och en enda prenumeration. Ett ämne accepterar upp till 1 000 samtidiga anslutningar från avsändare, medan prenumerationen som tar emot en ytterligare 1000 samtidiga anslutningar från mottagare. Om fler än 1000 samtidiga avsändare krävs, ska avsändare skicka meddelanden till Service Bus-protokollet via HTTP.

Du kan maximera genomflödet, utför du följande steg:

* Om varje avsändare finns i en annan process, kan du använda en enda fabrik processer.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard batchbearbetning intervall på 20 ms för att minska antalet överföringar för Service Bus klient-protokollet.
* Lämna gruppbaserade store-åtkomst aktiverat. Den här åtkomsten ökar den totala frekvensen med vilken meddelanden kan skrivas till kön eller ämnet.
* Ange antalet prefetch till 20 gånger de maximala hastighetsprestanda för alla mottagare av en fabrik. Det här antalet minskar antalet överföringar för Service Bus klient-protokollet.
* Använd en partitionerad kö för bättre prestanda och tillgänglighet.

### <a name="queue-with-a-large-number-of-receivers"></a>Kö med ett stort antal mottagare

Mål: Maximera receive frekvensen för en kö eller prenumeration med ett stort antal mottagare. Varje mottagare tar emot meddelanden med en begränsad hastighet. Antalet avsändare är liten.

Service Bus ger upp till 1 000 samtidiga anslutningar till en entitet. Om en kö kräver mer än 1000 mottagare, ersätter du kön med ett ämne och flera prenumerationer. Varje prenumeration har stöd för upp till 1000 samtidiga anslutningar. Mottagare kan också komma åt kö via HTTP-protokollet.

Du kan maximera genomflödet, gör du följande:

* Om varje mottagare finns i en annan process, kan du använda en enda fabrik processer.
* Mottagare kan använda synkron eller asynkron åtgärder. Måttlig receive frekvensen för en enskild mottagare får påverkar klientsidan batchbearbetning för en fullständig begäran inte mottagare dataflöde.
* Lämna gruppbaserade store-åtkomst aktiverat. Den här åtkomsten minskar den allmänna belastningen på entiteten. Det minskar också av övergripande frekvens med vilken meddelanden kan skrivas till kön eller ämnet.
* Ange antalet prefetch till ett litet värde (till exempel PrefetchCount = 10). Det här antalet förhindrar att mottagarna inaktivitet medan andra mottagare har stora mängder meddelanden som cachelagras.
* Använd en partitionerad kö för bättre prestanda och tillgänglighet.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Ämne med ett litet antal prenumerationer

Mål: Maximera dataflödet för ett ämne med ett litet antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att kombinerade receive-frekvens över alla prenumerationer som är större än frekvensen skicka. Antalet avsändare är liten. Antal mottagare per prenumeration är liten.

Du kan maximera genomflödet, gör du följande:

* Använda flera meddelandefabriker för att skapa avsändare för att öka den totala frekvensen för Skicka till ämnet. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* För att öka den totala frekvensen ta emot från en prenumeration kan du använda flera meddelandefabriker för att skapa mottagare. Använd asynkrona åtgärder eller flera trådar för varje mottagare.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard batchbearbetning intervall på 20 ms för att minska antalet överföringar för Service Bus klient-protokollet.
* Lämna gruppbaserade store-åtkomst aktiverat. Den här åtkomsten ökar den totala frekvensen som meddelanden kan skrivas till ämnet.
* Ange antalet prefetch till 20 gånger de maximala hastighetsprestanda för alla mottagare av en fabrik. Det här antalet minskar antalet överföringar för Service Bus klient-protokollet.
* Använd ett partitionerade ämne för bättre prestanda och tillgänglighet.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Ämne med ett stort antal prenumerationer

Mål: Maximera dataflödet för ett ämne med ett stort antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade receive hastigheten över alla prenumerationer som är mycket större än den skicka. Antalet avsändare är liten. Antal mottagare per prenumeration är liten.

Avsnitt med ett stort antal prenumerationer exponera vanligtvis ett låga övergripande dataflöde om dirigeras alla meddelanden till alla prenumerationer. Den här lågt dataflöde beror på det faktum att varje meddelande tas emot många gånger, och alla meddelanden som finns i ett ämne och alla dess prenumerationer lagras i samma lager. Det antas att antalet avsändare och antal mottagare per prenumeration är liten. Service Bus stöder upp till 2 000 prenumerationer per ämne.

Du kan maximera genomflödet, kan du prova följande steg:

* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard batchbearbetning intervall på 20 ms för att minska antalet överföringar för Service Bus klient-protokollet.
* Lämna gruppbaserade store-åtkomst aktiverat. Den här åtkomsten ökar den totala frekvensen som meddelanden kan skrivas till ämnet.
* Ange antalet prefetch till 20 gånger det förväntade ta emot priset på några sekunder. Det här antalet minskar antalet överföringar för Service Bus klient-protokollet.
* Använd ett partitionerade ämne för bättre prestanda och tillgänglighet.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar prestanda för Service Bus finns [partitionerade meddelandeentiteter][Partitioned messaging entities].

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
