---
title: Metodtips för att förbättra prestanda med Hjälp av Azure Service Bus
description: Beskriver hur du använder Service Bus för att optimera prestanda vid utbyte av förmedlade meddelanden.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: b864f433c67d47b4b92a1d4b98693ebd42806dd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259466"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Bra metoder för att öka prestanda med hjälp av meddelanden i Service Bus

I den här artikeln beskrivs hur du använder Azure Service Bus för att optimera prestanda vid utbyte av förmedlade meddelanden. Den första delen av den här artikeln beskriver de olika mekanismer som erbjuds för att öka prestanda. Den andra delen ger vägledning om hur du använder Service Bus på ett sätt som kan erbjuda bästa prestanda i ett visst scenario.

I den här artikeln refererar termen "klient" till alla entiteter som har åtkomst till Service Bus. En klient kan ta rollen som en avsändare eller mottagare. Termen "avsändare" används för en Service Bus-kö eller ämnesklient som skickar meddelanden till en Service Bus-kö eller ämnesprenumeration. Termen "mottagare" avser en Service Bus-kö eller prenumerationsklient som tar emot meddelanden från en servicebusskö eller prenumeration.

De här avsnitten innehåller flera koncept som Service Bus använder för att öka prestandan.

## <a name="protocols"></a>Protokoll

Service Bus gör det möjligt för klienter att skicka och ta emot meddelanden via ett av tre protokoll:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. HTTP (Hypertext Transfer Protocol)

AMQP är den mest effektiva, eftersom den upprätthåller anslutningen till Service Bus. Det genomför också batching och prefetching. Om inte uttryckligen nämns förutsätter allt innehåll i den här artikeln att använda AMQP eller SBMP.

> [!IMPORTANT]
> SBMP är endast tillgängligt för .NET Framework. AMQP är standard för .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Välja lämplig servicebuss .NET SDK

Det finns två Azure Service Bus .NET SDK:er som stöds. Deras API: er är mycket lika, och det kan vara förvirrande vilken man ska välja. Se följande tabell för att vägleda ditt beslut. Vi föreslår Microsoft.Azure.ServiceBus SDK eftersom det är modernare, högpresterande och är plattformsoberoende kompatibelt. Dessutom stöder den AMQP över WebSockets och är en del av Azure .NET SDK-samlingen med projekt med öppen källkod.

| NuGet-paketet | Primära namnområden | Minsta plattform(er) | Protokoll |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET-ramverket 4.6.1<br>Mono 5,4<br>Xamarin.iOS 10,14<br>Xamarin.Mac 3,8<br>Xamarin.Android 8.0<br>Universell Windows-plattform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET-ramverket 4.6.1 | AMQP<br>SBMP (på andra sätt)<br>HTTP |

Mer information om minsta stöd för .NET Standard-plattformen finns i [.NET-implementeringsstöd](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Återanvända fabriker och kunder

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Service Bus-klientobjekt, till [`IQueueClient`][QueueClient] exempel [`IMessageSender`][MessageSender]implementeringar av eller , bör registreras för beroendeinjektion som singletons (eller instansieras en gång och delas). Vi rekommenderar att du inte stänger meddelandefabriker eller kö-, ämnes- och prenumerationsklienter när du har skickat ett meddelande och sedan återskapar dem när du skickar nästa meddelande. Om du stänger en meddelandefabrik tas anslutningen till servicebusstjänsten bort och en ny anslutning upprättas när fabriken återskapas. Att upprätta en anslutning är en dyr åtgärd som du kan undvika genom att återanvända samma fabriks- och klientobjekt för flera åtgärder. Du kan säkert använda dessa klientobjekt för samtidiga asynkrona åtgärder och från flera trådar.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Service Bus-klientobjekt, `QueueClient` `MessageSender`till exempel eller , skapas via ett [MessagingFactory-objekt,][MessagingFactory] vilket också ger intern hantering av anslutningar. Vi rekommenderar att du inte stänger meddelandefabriker eller kö-, ämnes- och prenumerationsklienter när du har skickat ett meddelande och sedan återskapar dem när du skickar nästa meddelande. Om du stänger en meddelandefabrik tas anslutningen till servicebusstjänsten bort och en ny anslutning upprättas när fabriken återskapas. Att upprätta en anslutning är en dyr åtgärd som du kan undvika genom att återanvända samma fabriks- och klientobjekt för flera åtgärder. Du kan säkert använda dessa klientobjekt för samtidiga asynkrona åtgärder och från flera trådar.

---

## <a name="concurrent-operations"></a>Samtidiga operationer

Det tar lite tid att utföra en åtgärd (skicka, ta emot, ta bort osv.). Den här gången ingår bearbetning av åtgärden av Service Bus-tjänsten utöver svarstiden för begäran och svaret. Om du vill öka antalet åtgärder per gång måste åtgärder utföras samtidigt.

Klienten schemalägger samtidiga åtgärder genom att utföra asynkrona åtgärder. Nästa begäran startas innan den tidigare begäran har slutförts. Följande kodavsnitt är ett exempel på en asynkron skicka-åtgärd:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Följande kod är ett exempel på en asynkron mottagningsåtgärd.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Se GitHub-databasen för fullständiga <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">källkodsexempel: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Objektet `MessageReceiver` instansieras med anslutningssträngen, könamnet och ett peek-look-mottagningsläge. Därefter används `receiver` instansen för att registrera meddelandehanteraren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Se GitHub-databasen för fullständiga <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">källkodsexempel: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Skapar `MessagingFactory` ett `factory` objekt från anslutningssträngen. Med `factory` instansieras `MessageReceiver` en instansieras. Därefter används `receiver` instansen för att registrera hanteraren i meddelandet.

---

## <a name="receive-mode"></a>Ta emot läge

När du skapar en kö- eller prenumerationsklient kan du ange ett mottagningsläge: *Peek-lock* eller *Ta emot och ta bort*. Standardinställningen för `PeekLock`mottagning är . När du arbetar i standardläge skickar klienten en begäran om att få ett meddelande från Service Bus. När klienten har tagit emot meddelandet skickar den en begäran om att slutföra meddelandet.

När du ställer `ReceiveAndDelete`in mottagningsläget till kombineras båda stegen i en enda begäran. De här stegen minskar det totala antalet åtgärder och kan förbättra det övergripande meddelandeflödet. Denna prestandavinst riskerar att förlora meddelanden.

Service Bus stöder inte transaktioner för mottagnings- och borttagningsåtgärder. Dessutom krävs peek-lock semantik för alla scenarier där klienten vill skjuta upp eller göra ett meddelande [i ett meddelande.](service-bus-dead-letter-queues.md)

## <a name="client-side-batching"></a>Batchbearbetning på klientsidan

Batchbearbetning på klientsidan gör det möjligt för en kö- eller ämnesklient att fördröja sändningen av ett meddelande under en viss tidsperiod. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. Batchbearbetning på klientsidan medför också att en **Complete** kö- eller prenumerationsklient batchar flera Slutför-begäranden till en enda begäran. Batchbearbetning är endast tillgängligt för asynkrona **skicka** och **slutför-åtgärder.** Synkrona åtgärder skickas omedelbart till servicebusstjänsten. Batchbearbetning sker inte för peek eller ta emot åtgärder, inte heller batchbearbetning sker över klienter.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Batching-funktionen för .NET Standard SDK, utsätter ännu inte en egenskap för att manipulera.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Som standard använder en klient ett batchintervall på 20 ms. Du kan ändra batchintervallet genom att ange egenskapen [BatchFlushInterval][BatchFlushInterval] innan du skapar meddelandefabriken. Den här inställningen påverkar alla klienter som skapas av den här fabriken.

Om du vill inaktivera batchbearbetning ställer du in egenskapen [BatchFlushInterval][BatchFlushInterval] på **TimeSpan.Zero**. Ett exempel:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Batchbearbetning påverkar inte antalet fakturerbara meddelandeåtgärder och är endast tillgängligt för Service Bus-klientprotokollet med [microsoft.servicebus.messaging-biblioteket.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) HTTP-protokollet stöder inte batchbearbetning.

> [!NOTE]
> Inställningen `BatchFlushInterval` säkerställer att batchningen är implicit från programmets perspektiv. dvs. programmet ringer `SendAsync` `CompleteAsync` och ringer och inte gör specifika Batch-samtal.
>
> Explicit batchbearbetning på klientsidan kan implementeras genom att använda nedanstående metodanrop:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Här måste den sammanlagda storleken på meddelandena vara mindre än den maximala storleken som stöds av prisnivån.

---

## <a name="batching-store-access"></a>Batching butik tillgång

För att öka dataflödet för en kö, ett ämne eller en prenumeration batchar Service Bus flera meddelanden när den skriver till sitt interna arkiv. Om det är aktiverat i en kö eller ett ämne batchas det att skriva meddelanden till arkivet. Om det är aktiverat i en kö eller prenumeration batchars om du tar bort meddelanden från arkivet. Om batchad butiksåtkomst är aktiverad för en entitet försenar Service Bus en butiksskrivningsåtgärd för den entiteten med upp till 20 ms.

> [!NOTE]
> Det finns ingen risk att förlora meddelanden med batchbearbetning, även om det finns ett Service Bus-fel i slutet av ett batchintervall på 20 ms.

Ytterligare butiksåtgärder som inträffar under det här intervallet läggs till i batchen. Batchad butiksåtkomst påverkar endast **skicka** och **fullständig** verksamhet. inte påverkas. Batchad butiksåtkomst är en egenskap på en entitet. Batchbearbetning sker över alla entiteter som aktiverar batchad butiksåtkomst.

När du skapar en ny kö, ett ämne eller en ny prenumeration aktiveras batchad butiksåtkomst som standard.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Om du vill inaktivera batchad butiksåtkomst behöver `ManagementClient`du en instans av en . Skapa en kö från en `EnableBatchedOperations` köbeskrivning `false`som anger egenskapen till .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Mer information finns i följande:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Om du vill inaktivera batchad butiksåtkomst behöver `NamespaceManager`du en instans av en . Skapa en kö från en `EnableBatchedOperations` köbeskrivning `false`som anger egenskapen till .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Mer information finns i följande:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Batchad butiksåtkomst påverkar inte antalet fakturerbara meddelandeåtgärder och tillhör en kö, ett ämne eller en prenumeration. Den är oberoende av mottagningsläget och protokollet som används mellan en klient och servicebusstjänsten.

## <a name="prefetching"></a>Prefetching

[Genom prefetching](service-bus-prefetch.md) kan kö- eller prenumerationsklienten läsa in ytterligare meddelanden från tjänsten när den utför en mottagningsåtgärd. Klienten lagrar dessa meddelanden i en lokal cache. Storleken på cacheminnet bestäms `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` av egenskaperna eller. Varje klient som möjliggör prefetching behåller sin egen cache. En cache delas inte mellan klienter. Om klienten initierar en mottagningsåtgärd och cacheminnet är tomt, överför tjänsten en bunt meddelanden. Storleken på batchen är lika med storleken på cachen eller 256 KB, beroende på vilket som är mindre. Om klienten initierar en mottagningsåtgärd och cacheminnet innehåller ett meddelande hämtas meddelandet från cachen.

När ett meddelande är förbestämt låser tjänsten det försökt meddelandet. Med låset kan det försökt meddelandet inte tas emot av en annan mottagare. Om mottagaren inte kan slutföra meddelandet innan låset går ut blir meddelandet tillgängligt för andra mottagare. Den försökt kopian av meddelandet finns kvar i cacheminnet. Mottagaren som förbrukar den utgångna cachelagrade kopian får ett undantag när den försöker slutföra meddelandet. Som standard upphör meddelandelåset efter 60 sekunder. Det här värdet kan utökas till 5 minuter. För att förhindra förbrukning av utgångna meddelanden bör cachestorleken alltid vara mindre än antalet meddelanden som kan förbrukas av en klient inom låstidsintervallet.

När du använder standardlåset förfaller 60 `PrefetchCount` sekunder, är ett bra värde för 20 gånger den maximala bearbetningshastigheten för alla mottagare av fabriken. En fabrik skapar till exempel tre mottagare och varje mottagare kan bearbeta upp till 10 meddelanden per sekund. Prefetch-antalet bör inte överstiga 20 X 3 X 10 = 600. Som standard `PrefetchCount` är värdet 0, vilket innebär att inga ytterligare meddelanden hämtas från tjänsten.

Prefetching-meddelanden ökar det totala dataflödet för en kö eller prenumeration eftersom det minskar det totala antalet meddelandeåtgärder eller rundresor. Det tar dock längre tid att hämta det första meddelandet (på grund av den ökade meddelandestorleken). Ta emot prefetched meddelanden kommer att vara snabbare eftersom dessa meddelanden redan har hämtats av klienten.

Egenskapen Time-to-live (TTL) för ett meddelande kontrolleras av servern när servern skickar meddelandet till klienten. Klienten kontrollerar inte meddelandets TTL-egenskap när meddelandet tas emot. I stället kan meddelandet tas emot även om meddelandets TTL har skickats medan meddelandet cachelagrades av klienten.

Prefetching påverkar inte antalet fakturerbara meddelandeåtgärder och är endast tillgängligt för Service Bus-klientprotokollet. HTTP-protokollet stöder inte prefetching. Prefetching är tillgängligt för både synkrona och asynkrona mottagningsåtgärder.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Mer information finns i `PrefetchCount` följande egenskaper:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Mer information finns i `PrefetchCount` följande egenskaper:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Prefetching och ReceiveBatch

> [!NOTE]
> Det här avsnittet gäller endast för WindowsAzure.ServiceBus SDK, eftersom Microsoft.Azure.ServiceBus SDK inte exponerar batchfunktioner.

Medan begreppen prefetching flera meddelanden tillsammans har liknande semantik till`ReceiveBatch`bearbetning av meddelanden i ett parti ( ), det finns några mindre skillnader som måste hållas i åtanke när utnyttja dessa tillsammans.

Prefetch är en konfiguration (eller läge) `SubscriptionClient`på `ReceiveBatch` klienten (`QueueClient` och ) och är en åtgärd (som har semantik för begäran-svar).

När du använder dessa tillsammans bör du överväga följande fall -

* Prefetch bör vara större än eller lika med antalet `ReceiveBatch`meddelanden som du förväntar dig att ta emot från .
* Prefetch kan vara upp till n/3 gånger antalet meddelanden som bearbetas per sekund, där n är standardlåsvaraktigheten.

Det finns vissa utmaningar med att ha en girig strategi (dvs. att hålla prefetch räkna mycket hög), eftersom det innebär att meddelandet är låst till en viss mottagare. Rekommendationen är att prova prefetch värden mellan de tröskelvärden som nämns ovan och empiriskt identifiera vad som passar.

## <a name="multiple-queues"></a>Flera köer

Om den förväntade belastningen inte kan hanteras av en enda kö eller ett enda ämne måste du använda flera meddelandeentiteter. När du använder flera entiteter skapar du en dedikerad klient för varje entitet, i stället för att använda samma klient för alla entiteter.

## <a name="development-and-testing-features"></a>Utvecklings- och testfunktioner

> [!NOTE]
> Det här avsnittet gäller endast för WindowsAzure.ServiceBus SDK, eftersom Microsoft.Azure.ServiceBus SDK inte exponerar den här funktionen.

Service Bus har en funktion, som används speciellt för utveckling, [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering]som aldrig bör användas i **produktionskonfigurationer:**.

När nya regler eller filter läggs till [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] i avsnittet kan du använda för att kontrollera att det nya filteruttrycket fungerar som förväntat.

## <a name="scenarios"></a>Scenarier

I följande avsnitt beskrivs typiska meddelandescenarier och de önskade servicebussinställningarna beskrivs. Dataflödeshastigheter klassificeras som små (mindre än 1 meddelande/sekund), måttlig (1 meddelande/sekund eller större men mindre än 100 meddelanden/sekund) och hög (100 meddelanden/sekund eller högre). Antalet klienter klassificeras som små (5 eller färre), måttlig (mer än 5 men mindre än eller lika med 20) och stora (mer än 20).

### <a name="high-throughput-queue"></a>Kö med hög dataflöde

Mål: Maximera dataflödet för en enda kö. Antalet avsändare och mottagare är litet.

* Om du vill öka den totala sändningshastigheten i kön använder du flera meddelandefabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Om du vill öka den totala mottagningsfrekvensen från kön använder du flera meddelandefabriker för att skapa mottagare.
* Använd asynkrona åtgärder för att dra nytta av batchbearbetning på klientsidan.
* Ställ in batchintervallet till 50 ms för att minska antalet Service Bus-klientprotokollöverföringar. Om flera avsändare används ökar du batchintervallet till 100 ms.
* Lämna batchad butiksåtkomst aktiverad. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas in i kön.
* Ställ in prefetchantalet till 20 gånger den maximala bearbetningshastigheten för alla mottagare av en fabrik. Detta antal minskar antalet Service Bus klientprotokollöverföringar.

### <a name="multiple-high-throughput-queues"></a>Flera köer med hög dataflöde

Mål: Maximera det totala dataflödet för flera köer. Dataflödet för en enskild kö är måttlig eller hög.

Om du vill ha maximalt dataflöde i flera köer använder du inställningarna som beskrivs för att maximera dataflödet för en enda kö. Dessutom kan du använda olika fabriker för att skapa klienter som skickar eller ta emot från olika köer.

### <a name="low-latency-queue"></a>Kö för låg latens

Mål: Minimera svarstiden från slut-till-slut på ett kö eller ämne. Antalet avsändare och mottagare är litet. Köns dataflöde är litet eller måttligt.

* Inaktivera batchbearbetning på klientsidan. Klienten skickar omedelbart ett meddelande.
* Inaktivera batchad butiksåtkomst. Tjänsten skriver omedelbart meddelandet till butiken.
* Om du använder en enda klient ställer du in prefetchantalet till 20 gånger mottagarens bearbetningshastighet. Om flera meddelanden anländer till kön samtidigt överför Service Bus-klientprotokollet dem alla samtidigt. När klienten tar emot nästa meddelande finns det meddelandet redan i den lokala cachen. Cachen ska vara liten.
* Om du använder flera klienter ställer du in antalet prefetch till 0. Genom att ange antalet kan den andra klienten ta emot det andra meddelandet medan den första klienten fortfarande bearbetar det första meddelandet.

### <a name="queue-with-a-large-number-of-senders"></a>Kö med ett stort antal avsändare

Mål: Maximera dataflödet för en kö eller ett ämne med ett stort antal avsändare. Varje avsändare skickar meddelanden med en måttlig hastighet. Antalet mottagare är litet.

Service Bus möjliggör upp till 1 000 samtidiga anslutningar till en meddelandeenhet (eller 5000 med AMQP). Den här gränsen tillämpas på namnområdesnivå och köer/ämnen/prenumerationer begränsas av gränsen för samtidiga anslutningar per namnområde. För köer delas det här numret mellan avsändare och mottagare. Om alla 1 000 anslutningar krävs för avsändare ersätter du kön med ett ämne och en enda prenumeration. Ett ämne accepterar upp till 1 000 samtidiga anslutningar från avsändare, medan prenumerationen accepterar ytterligare 1000 samtidiga anslutningar från mottagare. Om fler än 1 000 samtidiga avsändare krävs ska avsändare skicka meddelanden till Service Bus-protokollet via HTTP.

Så här maximerar du dataflödet:

* Om varje avsändare finns i en annan process använder du bara en enda fabrik per process.
* Använd asynkrona åtgärder för att dra nytta av batchbearbetning på klientsidan.
* Använd standardbatchintervallet på 20 ms för att minska antalet Service Bus-klientprotokollöverföringar.
* Lämna batchad butiksåtkomst aktiverad. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas in i kön eller ämnet.
* Ställ in prefetchantalet till 20 gånger den maximala bearbetningshastigheten för alla mottagare av en fabrik. Detta antal minskar antalet Service Bus klientprotokollöverföringar.

### <a name="queue-with-a-large-number-of-receivers"></a>Kö med ett stort antal mottagare

Mål: Maximera mottagningsfrekvensen för en kö eller prenumeration med ett stort antal mottagare. Varje mottagare tar emot meddelanden med en måttlig hastighet. Antalet avsändare är litet.

Service Bus möjliggör upp till 1000 samtidiga anslutningar till en entitet. Om en kö kräver fler än 1 000 mottagare ersätter du kön med ett ämne och flera prenumerationer. Varje prenumeration kan stödja upp till 1000 samtidiga anslutningar. Alternativt kan mottagare komma åt kön via HTTP-protokollet.

Gör så här för att maximera dataflödet:

* Om varje mottagare finns i en annan process använder du bara en enda fabrik per process.
* Mottagare kan använda synkrona eller asynkrona åtgärder. Med tanke på den måttliga mottagningsfrekvensen för en enskild mottagare påverkar batchbearbetning på klientsidan av en fullständig begäran inte mottagarens dataflöde.
* Lämna batchad butiksåtkomst aktiverad. Den här åtkomsten minskar den totala belastningen för entiteten. Det minskar också den totala hastigheten med vilken meddelanden kan skrivas in i kön eller ämnet.
* Ange antalet prefetch till ett litet värde (till exempel PrefetchCount = 10). Detta antal förhindrar att mottagare är inaktiva medan andra mottagare har ett stort antal meddelanden cachelagrade.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Ämne med ett litet antal prenumerationer

Mål: Maximera dataflödet för ett ämne med ett litet antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade mottagningsfrekvensen för alla prenumerationer är större än sändningshastigheten. Antalet avsändare är litet. Antalet mottagare per prenumeration är litet.

Gör så här för att maximera dataflödet:

* Om du vill öka den totala sändningshastigheten i avsnittet använder du flera meddelandefabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Om du vill öka den totala mottagningsfrekvensen från en prenumeration använder du flera meddelandefabriker för att skapa mottagare. Använd asynkrona åtgärder eller flera trådar för varje mottagare.
* Använd asynkrona åtgärder för att dra nytta av batchbearbetning på klientsidan.
* Använd standardbatchintervallet på 20 ms för att minska antalet Service Bus-klientprotokollöverföringar.
* Lämna batchad butiksåtkomst aktiverad. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas in i ämnet.
* Ställ in prefetchantalet till 20 gånger den maximala bearbetningshastigheten för alla mottagare av en fabrik. Detta antal minskar antalet Service Bus klientprotokollöverföringar.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Ämne med ett stort antal prenumerationer

Mål: Maximera dataflödet för ett ämne med ett stort antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade mottagningsfrekvensen för alla prenumerationer är mycket större än sändningshastigheten. Antalet avsändare är litet. Antalet mottagare per prenumeration är litet.

Ämnen med ett stort antal prenumerationer visar vanligtvis ett lågt övergripande dataflöde om alla meddelanden dirigeras till alla prenumerationer. Det här lågdataflödet orsakas av det faktum att varje meddelande tas emot många gånger och alla meddelanden som finns i ett ämne och alla dess prenumerationer lagras i samma butik. Det antas att antalet avsändare och antal mottagare per prenumeration är litet. Service Bus stöder upp till 2 000 prenumerationer per ämne.

För att maximera dataflödet kan du prova följande steg:

* Använd asynkrona åtgärder för att dra nytta av batchbearbetning på klientsidan.
* Använd standardbatchintervallet på 20 ms för att minska antalet Service Bus-klientprotokollöverföringar.
* Lämna batchad butiksåtkomst aktiverad. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas in i ämnet.
* Ställ in antalet prefetch till 20 gånger den förväntade mottagningsfrekvensen i sekunder. Detta antal minskar antalet Service Bus klientprotokollöverföringar.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md