---
title: "Bästa praxis för att förbättra prestanda med hjälp av Azure Service Bus | Microsoft Docs"
description: "Beskriver hur du använder Service Bus för att optimera prestanda vid utbyte av asynkrona meddelanden."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e756c15d-31fc-45c0-8df4-0bca0da10bb2
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: aba53fcadb9cefa70afc175dd02e4723eb6e5f5d
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Metodtips för bättre prestanda med hjälp av Service Bus-meddelanden

Den här artikeln beskriver hur du använder [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) att optimera prestanda vid utbyte av asynkrona meddelanden. Den första delen av den här artikeln beskriver de olika metoder som erbjuds för att öka prestanda. Den andra delen innehåller råd om hur du använder Service Bus på ett sätt som kan ge bästa möjliga prestanda i ett visst scenario.

I det här avsnittet avser termen ”klient” entiteter som har åtkomst till Service Bus. En klient kan ta rollen för en avsändare eller en mottagare. Termen ”avsändare” används för en Service Bus kö eller ett ämne-klient som skickar meddelanden till en Service Bus-kö eller ett ämne prenumeration. Termen ”mottagaren” refererar till en Service Bus kö eller prenumeration klient som tar emot meddelanden från en Service Bus-kö eller prenumeration.

Dessa avsnitt beskrivs några begrepp som Service Bus använder för att förbättra prestanda.

## <a name="protocols"></a>Protokoll

Service Bus gör att klienter kan skicka och ta emot meddelanden via ett av tre protokoll:

1. Avancerade Message Queuing-protokollet (AMQP)
2. Service Bus-meddelanden Protocol (SBMP)
3. HTTP

AMQP och SBMP är effektivare, eftersom de upprätthålla anslutningen till Service Bus så länge meddelandefabrik finns. Den implementerar batchbearbetning och förhämtar. Om du inte uttryckligen anges förutsätter allt innehåll i det här avsnittet att använda AMQP eller SBMP.

## <a name="reusing-factories-and-clients"></a>Återanvända fabriker och klienter

Service Bus klienten objekt, till exempel [QueueClient] [ QueueClient] eller [MessageSender][MessageSender], skapas via en [ MessagingFactory] [ MessagingFactory] -objektet, vilket ger också intern hantering av anslutningar. Du bör inte stänga meddelanden fabriker eller kön, ämnet och prenumerationen klienter när du skickar ett meddelande och sedan återskapa dem när du skickar nästa meddelande. Stänga en meddelandefabrik tar bort anslutningen till Service Bus-tjänst och en ny anslutning upprättas när återskapa fabriken. Upprätta en anslutning är en kostsam åtgärd som du kan undvika genom att återanvända samma fabrik och klientobjekt för flera åtgärder. Du kan använda på ett säkert sätt den [QueueClient] [ QueueClient] objekt för att skicka meddelanden från samtidiga asynkrona åtgärder och flera trådar. 

## <a name="concurrent-operations"></a>Samtidiga åtgärder

Utföra en åtgärd (skicka, ta emot, ta bort, etc.) tar en stund. Den här gången innefattar bearbetning av åtgärden av tjänsten Service Bus förutom svarstid för begäran och svar. Om du vill öka antalet åtgärder per tid måste operations köras samtidigt. Du kan göra detta på flera olika sätt:

* **Asynkrona åtgärder**: klienten schemalägger åtgärder genom att utföra asynkrona åtgärder. Nästa förfrågan har startat innan den förra begäranden har slutförts. Följande är ett exempel på en asynkron sändningsåtgärd:
  
 ```csharp
  BrokeredMessage m1 = new BrokeredMessage(body);
  BrokeredMessage m2 = new BrokeredMessage(body);
  
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
  
  Detta är ett exempel på en asynkron mottagning igen:
  
  ```csharp
  Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  
  Task.WaitAll(receive1, receive2);
  Console.WriteLine("All messages received");
  
  async void ProcessReceivedMessage(Task<BrokeredMessage> t)
  {
    BrokeredMessage m = t.Result;
    Console.WriteLine("{0} received", m.Label);
    await m.CompleteAsync();
    Console.WriteLine("{0} complete", m.Label);
  }
  ```

* **Flera fabriker**: alla klienter (avsändare förutom mottagare) som skapats av samma fabrik dela en TCP-anslutning. Den maximala genomströmningen begränsas av antal åtgärder som kan gå igenom den här TCP-anslutningen. Dataflödet som kan hämtas med en enda fabrik varierar kraftigt med TCP fram och åter gånger och meddelandestorlek. Om du vill använda högre hastigheter, bör du använda flera meddelanden fabriker.

## <a name="receive-mode"></a>Mottagningsläge

När du skapar en kö eller prenumeration klient kan du ange en receive-läge: *titt Lås* eller *ta emot och ta bort*. Standard mottagningsläge är [PeekLock][PeekLock]. När det här läget kan skickar klienten en begäran om att ta emot ett meddelande från Service Bus. När klienten har tagit emot meddelandet, skickar en begäran om att slutföra meddelandet.

När du ställer in receive-läge [ReceiveAndDelete][ReceiveAndDelete], båda stegen kombineras i en enskild begäran. Detta minskar det totala antalet åtgärder och kan förbättra den övergripande genomströmningen. Den här prestandafördelar kommer dess risk att förlora meddelanden.

Service Bus stöder inte transaktioner för ta emot och delete-åtgärder. Dessutom titt Lås semantik krävs för alla scenarier där klienten vill skjuta upp eller [förlorade](service-bus-dead-letter-queues.md) ett meddelande.

## <a name="client-side-batching"></a>Klientsidans batchbearbetning

Klientsidans batchbearbetning gör en kö eller ett ämne klienten att fördröja skickas ett meddelande för en viss tidsperiod. Om klienten skickar ytterligare meddelanden under den här tiden, skickar den meddelanden i en enskild batch. Klientsidans batchbearbetning gör att en kö eller prenumeration klient till batchen flera **Slutför** begäranden till en enskild begäran. Batchbearbetning är endast tillgängligt för asynkron **skicka** och **Slutför** åtgärder. Synkrona åtgärder skickas direkt till tjänsten Service Bus. Batchbearbetning inte ske för titt eller ta emot operations eller batchbearbetning sker över klienter.

Som standard använder en klient en batch-intervallet för 20ms. Du kan ändra batch-intervall genom att ange den [BatchFlushInterval] [ BatchFlushInterval] egenskapen innan du skapar meddelandefabrik. Den här inställningen påverkar alla klienter som har skapats med den här fabriken.

Om du vill inaktivera batchbearbetning, ange den [BatchFlushInterval] [ BatchFlushInterval] egenskapen **TimeSpan.Zero**. Exempel:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Batchbearbetning påverkar inte antalet fakturerbar asynkrona åtgärder och är bara tillgängligt för Service Bus-klientprotokollet. HTTP-protokollet har inte stöd för batchbearbetning.

## <a name="batching-store-access"></a>Batchbearbetning store-åtkomst

Om du vill öka genomflödet av kön, ämnet och prenumerationen batchar Service Bus flera meddelanden vid skrivning till interna arkivet. Om aktiverad på en kö eller ett ämne, kommer det att batchar att skriva meddelanden till arkivet. Om aktiverat på en kö eller en prenumeration, kommer det att batchar att ta bort meddelanden från store. Om gruppbaserad store-åtkomst är aktiverat för en entitet, fördröjningar Service Bus store skrivning om entiteten med upp till 20 MS. 

> [!NOTE]
> Det finns ingen risk för att förlora meddelanden med batchbearbetning, även om det finns ett Service Bus-fel i slutet av ett 20ms batching intervall. 

Ytterligare store-åtgärder som inträffar under det här intervallet har lagts till i gruppen. Batchar store åtkomst endast påverkar **skicka** och **Slutför** verksamhet, ta emot påverkas inte. Batch store-åtkomst är en egenskap för en entitet. Batchbearbetning sker över alla enheter som möjliggör gruppbaserad store-åtkomst.

När du skapar en ny kö, ämne eller en prenumeration kan är batch store-åtkomst aktiverat som standard. Om du vill inaktivera batch store-åtkomst, ange den [EnableBatchedOperations] [ EnableBatchedOperations] egenskapen **FALSKT** innan du skapar entiteten. Exempel:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Batch store-åtkomst påverkar inte antalet fakturerbar asynkrona åtgärder och är en egenskap för en kö, ämne eller prenumeration. Det är oberoende av receive-läge och protokoll som används mellan klienten och tjänsten Service Bus.

## <a name="prefetching"></a>Förhämtar

[Förhämtar](service-bus-prefetch.md) gör att kön eller prenumeration klienten att läsa in ytterligare meddelanden från tjänsten när en receive-åtgärd utförs. Klienten lagrar dessa meddelanden i ett lokalt cacheminne. Storleken på cacheminnet bestäms av den [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] eller [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] egenskaper. Varje klient som möjliggör förhämtar upprätthåller sin egen cache. En cache delas inte mellan klienter. Om klienten startar en receive-åtgärd och dess cachen är tom, skickar tjänsten en grupp med meddelanden. Storlek på batch är lika med storleken på cacheminnet eller 256 KB, beroende på vilket som är mindre. Om klienten startar en receive-åtgärd och cachen innehåller ett meddelande, tas meddelandet från cacheminnet.

När ett meddelande är prefetched låser tjänsten prefetched meddelandet. På så sätt kan inte prefetched meddelandet tas emot av en annan mottagare. Om mottagaren inte kan slutföra meddelandet innan låset upphör att gälla, blir meddelandet tillgänglig för andra mottagare. Prefetched kopia av meddelandet finns kvar i cacheminnet. Mottagaren som förbrukar utgångna cachelagrad kopia får ett undantag när den försöker slutföra meddelandet. Som standard upphör meddelandet låset efter 60 sekunder. Det här värdet kan utökas till 5 minuter. För att förhindra användningen av inaktuella meddelanden ska cachestorleken alltid vara mindre än antalet meddelanden som kan användas av en klient i lås-timeout-intervall.

När du använder standard Låsets upphörande 60 sekunder, en bra värde för [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] är 20 gånger högsta bearbetning av alla mottagare av fabriken. Till exempel en fabrik skapar 3 mottagare, och varje mottagare kan bearbeta upp till 10 meddelanden per sekund. Antalet prefetch får inte överstiga 20 X 3 X 10 = 600. Som standard [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] anges till 0, vilket innebär att inga fler meddelanden hämtas från tjänsten.

Förhämtar meddelanden ökar det totala genomflödet för en kö eller en prenumeration, eftersom det totala antalet meddelandeåtgärder eller sändningar. Hämtar det första meddelandet, men tar längre tid (på grund av ökade meddelandelagringsstorlek). Ta emot prefetched meddelanden är snabbare eftersom dessa meddelanden redan har laddats ned av klienten.

Egenskapen time to live (TTL) för ett meddelande som kontrolleras av servern när servern skickar meddelandet till klienten. Klienten kontrollerar inte TTL meddelandeegenskapen när meddelandet tas emot. I stället kan meddelandet tas emot även om meddelandets TTL har passerat medan meddelandet cachelagrades av klienten.

Förhämtar påverkar inte antalet fakturerbar asynkrona åtgärder och är bara tillgängligt för Service Bus-klientprotokollet. HTTP-protokollet stöder inte förhämtar. Förhämtar är tillgänglig för både synkrona och asynkrona får åtgärder.

## <a name="express-queues-and-topics"></a>Express köer och ämnen

Expressenheter aktivera högt genomflöde och svarstid för minskade scenarier och stöds endast i standardnivån för meddelanden. Entiteter som skapats i [Premium-namnområden](service-bus-premium-messaging.md) inte stöder alternativet express. Med snabb entiteter, om ett meddelande skickas till en kö eller ett ämne, lagras meddelandet omedelbart inte i meddelandearkiv. I stället cachelagras i minnet. Om ett meddelande som finns kvar i kön i mer än ett par sekunder, skrivs den automatiskt till stabil lagring, vilket skyddar mot dataförlust på grund av ett avbrott. Skrivning av meddelandet till en minnescache ökar dataflödet och minskar latens eftersom det finns ingen åtkomst till stabil lagring vid den tidpunkt som meddelandet har skickats. Meddelanden som används inom några sekunder skrivs inte till meddelandearkiv. I följande exempel skapas en uttrycklig avsnittet.

```csharp
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Om ett meddelande som innehåller viktig information som inte får vara förlorade skickas till en snabb entitet avsändaren kan tvinga Service Bus att direkt spara meddelandet till stabil lagring genom att ange den [ForcePersistence] [ ForcePersistence] egenskapen **SANT**.

> [!NOTE]
> Expressenheter stöder inte transaktioner.

## <a name="use-of-partitioned-queues-or-topics"></a>Användning av partitionerade köer och ämnen

Internt, Service Bus använder samma nod och messaging lagra bearbetas och lagras alla meddelanden för en meddelandeentitet (kö eller ett ämne). En [partitionerade kö eller ett ämne](service-bus-partitioning.md), å andra sidan distribueras över flera noder och meddelanden lagras. Partitionerade köer och ämnen inte bara att ge en bättre genomströmning än vanliga köer och ämnen, de också uppvisar en högre tillgänglighet. Så här skapar du en partitionerad entitet i [EnablePartitioning] [ EnablePartitioning] egenskapen **SANT**som visas i följande exempel. Mer information om partitionerade enheter finns [partitionerade meddelandeentiteter][Partitioned messaging entities].

```csharp
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Användning av flera köer

Om det går inte att använda en partitionerad kö eller ett ämne eller den förväntade belastningen kan inte hanteras av en enskild partitionerade kö eller ett ämne, måste du använda flera meddelandeentiteter. När du använder flera enheter, skapa en dedikerad klient för varje entitet i stället för att använda samma klient för alla entiteter.

## <a name="development-and-testing-features"></a>Utveckling och testning funktioner

Service Bus har en funktion som används för utveckling som **ska aldrig användas i produktion konfigurationer**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

När nya regler eller filter har lagts till i avsnittet, kan du använda [TopicDescription.EnableFilteringMessagesBeforePublishing][] och kontrollera att nya filteruttrycket fungerar som förväntat.

## <a name="scenarios"></a>Scenarier

I följande avsnitt beskrivs vanliga meddelandehantering och beskriver de föredragna inställningarna för Service Bus. Hastigheter klassificeras så liten (mindre än 1 meddelande per sekund), Måttlig (1-meddelande per sekund eller större men mindre än 100 meddelanden per sekund) och hög (100 meddelanden/andra eller senare). Antalet klienter som är klassificerade som liten (5 eller färre), Måttlig (mer än 5 men mindre än eller lika med 20), och stora (mer än 20).

### <a name="high-throughput-queue"></a>Hög genomströmning kön

Mål: Maximera genomströmningen i en enskild kö. Antalet avsändare och mottagare är liten.

* Använda en partitionerad kö för bättre prestanda och tillgänglighet.
* Använda flera meddelandefabriker för att skapa avsändare för att öka den övergripande överföringshastighet i kön. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Använda flera meddelandefabriker för att skapa mottagare för att öka den övergripande receive-hastigheten från kön.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Ange batching intervall till 50ms att minska antalet överföringar för Service Bus-klient-protokollet. Om du använder flera avsändare, öka batching intervallet ska 100ms.
* Lämna gruppbaserad store-åtkomst aktiverat. Detta ökar den övergripande hastighet som meddelanden kan skrivas i kön.
* Ange antalet prefetch till 20 gånger de högsta hastighetsprestanda av alla mottagare av ett fabriken. Detta minskar antalet överföringar för Service Bus-klient-protokollet.

### <a name="multiple-high-throughput-queues"></a>Flera hög genomströmning köer

Mål: Maximera totala genomflödet i flera köer. Dataflöde för en enskild kö är måttlig eller hög.

Hämta maximalt dataflöde i flera köer med de inställningar som anges för att maximera genomströmningen i en enskild kö. Dessutom kan använda olika fabriker för att skapa klienter som skicka eller ta emot från olika köer.

### <a name="low-latency-queue"></a>Låg latens kön

Mål: Minimera svarstiden för slutpunkt till slutpunkt för en kö eller ett ämne. Antalet avsändare och mottagare är liten. Dataflöde för kön är liten eller Måttlig.

* Använda en partitionerad kö för förbättrad tillgänglighet.
* Inaktivera klientens batchbearbetning. Skickar klienten omedelbart ett meddelande.
* Inaktivera batch store-åtkomst. Tjänsten skriver omedelbart meddelandet till arkivet.
* Om du använder en enskild klient kan du ange antalet prefetch till 20 gånger behandlingstakt för mottagaren. Om flera meddelanden anländer till kön samtidigt, överför Service Bus-klientprotokollet dem på samma gång. När klienten får nästa meddelande, finns meddelandet redan i den lokala cachen. Cachen bör vara små.
* Om du använder flera klienter, anger du antalet prefetch till 0. Genom att göra får andra klienten det andra meddelandet när den första klienten fortfarande bearbetar det första meddelandet.

### <a name="queue-with-a-large-number-of-senders"></a>Kö med ett stort antal avsändare

Mål: Maximera genomströmningen i en kö eller ett ämne med ett stort antal avsändare. Varje avsändare skickar meddelanden med en måttlig hastighet. Antalet mottagare är liten.

Service Bus kan upp till 1 000 samtidiga anslutningar till en meddelandeentitet (eller 5000 använder AMQP). Den här gränsen tillämpas på namnområdesnivån och köer-avsnitt-prenumerationer är begränsad av antalet samtidiga anslutningar per namnområde. För köer delas antalet mellan avsändare och mottagare. Om alla 1000 anslutningar krävs för avsändare, bör du ersätta kön med ett ämne och en enda prenumeration. Ett ämne accepterar upp till 1 000 samtidiga anslutningar från avsändare, medan prenumerationen accepterar en ytterligare 1000 samtidiga anslutningar från mottagare. Om högst 1 000 samtidiga avsändare krävs ska avsändare skicka meddelanden i Service Bus-protokollet via HTTP.

För att maximera genomströmningen, gör du följande:

* Använda en partitionerad kö för bättre prestanda och tillgänglighet.
* Om varje avsändare finns i en annan process, kan du använda en enda fabrik processer.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard-batchbearbetning intervall på 20 MS för att minska antalet överföringar för Service Bus-klient-protokollet.
* Lämna gruppbaserad store-åtkomst aktiverat. Detta ökar den övergripande hastighet som meddelanden kan skrivas till den kö eller ett ämne.
* Ange antalet prefetch till 20 gånger de högsta hastighetsprestanda av alla mottagare av ett fabriken. Detta minskar antalet överföringar för Service Bus-klient-protokollet.

### <a name="queue-with-a-large-number-of-receivers"></a>Kö med ett stort antal mottagare

Mål: Maximera receive frekvensen för en kö eller en prenumeration med ett stort antal mottagare. Varje mottagare tar emot meddelanden med en måttlig hastighet. Antal avsändare är liten.

Service Bus kan upp till 1 000 samtidiga anslutningar till en entitet. Om en kö kräver mer än 1000 mottagare, bör du ersätta kön med ett ämne och flera prenumerationer. Varje prenumeration kan stöda upp till 1000 samtidiga anslutningar. Mottagare kan också komma åt kön via HTTP-protokollet.

För att maximera genomströmningen, gör du följande:

* Använda en partitionerad kö för bättre prestanda och tillgänglighet.
* Om varje mottagare finns i en annan process, kan du använda en enda fabrik processer.
* Mottagare kan använda synkron eller asynkron åtgärder. Ges begränsad receive frekvensen för en enskild mottagare påverkar klientsidan massbearbetning av en fullständig begäran inte mottagaren genomflöde.
* Lämna gruppbaserad store-åtkomst aktiverat. Detta minskar den allmänna belastningen på enheten. Det minskar också den sammanlagda hastighet som meddelanden kan skrivas till den kö eller ett ämne.
* Ange antalet prefetch till ett mindre värde (till exempel PrefetchCount = 10). Detta förhindrar att mottagarna inaktivitet medan andra mottagare har stora mängder meddelanden som cachelagras.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Avsnittet med ett litet antal prenumerationer

Mål: Maximera genomströmningen i ett ämne med ett litet antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade receive-hastigheten över alla prenumerationer är större än frekvensen skicka. Antal avsändare är liten. Antalet mottagare per prenumeration är liten.

För att maximera genomströmningen, gör du följande:

* Använd en partitionerad avsnittet för bättre prestanda och tillgänglighet.
* Om du vill öka den övergripande överföringshastighet i avsnittet om att använda flera meddelandefabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* För att öka den övergripande receive-hastigheten från en prenumeration kan du använda flera meddelandefabriker för att skapa mottagare. Använda asynkrona åtgärder eller flera trådar för varje mottagare.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard-batchbearbetning intervall på 20 MS för att minska antalet överföringar för Service Bus-klient-protokollet.
* Lämna gruppbaserad store-åtkomst aktiverat. Detta ökar den övergripande hastighet som meddelanden kan skrivas i avsnittet.
* Ange antalet prefetch till 20 gånger de högsta hastighetsprestanda av alla mottagare av ett fabriken. Detta minskar antalet överföringar för Service Bus-klient-protokollet.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Avsnittet med ett stort antal prenumerationer

Mål: Maximera genomströmningen i ett ämne med ett stort antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade receive-hastigheten över alla prenumerationer är mycket större än överföringshastighet. Antal avsändare är liten. Antalet mottagare per prenumeration är liten.

Avsnitt med ett stort antal prenumerationer exponera en låg totala genomflödet vanligtvis om alla meddelanden dirigeras till alla prenumerationer. Detta beror på att varje meddelande tas emot många gånger, och alla meddelanden som finns i ett ämne och alla prenumerationer lagras i samma Arkiv. Det antas att antalet avsändare och mottagare per prenumeration antal är litet. Service Bus stöder upp till 2 000 prenumerationer per avsnitt.

För att maximera genomströmningen, gör du följande:

* Använd en partitionerad avsnittet för bättre prestanda och tillgänglighet.
* Använd asynkrona åtgärder för att dra nytta av klientsidan batchbearbetning.
* Använd standard-batchbearbetning intervall på 20 MS för att minska antalet överföringar för Service Bus-klient-protokollet.
* Lämna gruppbaserad store-åtkomst aktiverat. Detta ökar den övergripande hastighet som meddelanden kan skrivas i avsnittet.
* Ange antalet prefetch till 20 gånger den förväntade receive frekvensen i sekunder. Detta minskar antalet överföringar för Service Bus-klient-protokollet.

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
