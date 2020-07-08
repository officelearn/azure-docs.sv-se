---
title: Metod tips för att förbättra prestanda med hjälp av Azure Service Bus
description: Beskriver hur du använder Service Bus för att optimera prestanda vid utbyte av asynkrona meddelanden.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e0a6e54c1e941d7b7ff244ac40066a564e2ebbc4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341101"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Bra metoder för att öka prestanda med hjälp av meddelanden i Service Bus

Den här artikeln beskriver hur du använder Azure Service Bus för att optimera prestanda vid utbyte av asynkrona meddelanden. Den första delen av den här artikeln beskriver de olika mekanismer som erbjuds för att öka prestandan. Den andra delen ger vägledning om hur du använder Service Bus på ett sätt som kan erbjuda bästa prestanda i ett specifikt scenario.

I den här artikeln refererar termen "klient" till en entitet som har åtkomst till Service Bus. En klient kan utföra rollen som en avsändare eller mottagare. Termen "avsändare" används för en Service Bus kö eller ämnes klient som skickar meddelanden till en Service Bus kö eller en ämnes prenumeration. Termen "mottagare" syftar på en Service Bus kö eller en prenumerations klient som tar emot meddelanden från en Service Bus kö eller prenumeration.

Dessa avsnitt innehåller flera koncept som Service Bus används för att förbättra prestanda.

## <a name="protocols"></a>Protokoll

Service Bus gör det möjligt för klienter att skicka och ta emot meddelanden via ett av tre protokoll:

1. Advanced Message Queuing Protocol (AMQP)
2. SBMP (Service Bus Messaging Protocol)
3. HTTP (Hypertext Transfer Protocol)

AMQP är den mest effektiva, eftersom den upprätthåller anslutningen till Service Bus. Den implementerar även satsvis och för hämtning. Om inget annat anges förutsätter allt innehåll i den här artikeln användningen av AMQP eller SBMP.

> [!IMPORTANT]
> SBMP är endast tillgänglig för .NET Framework. AMQP är standardinställningen för .NET standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Välja lämplig Service Bus .NET SDK

Det finns två Azure Service Bus .NET-SDK: er som stöds. Deras API: er är mycket likartade och det kan vara förvirrande vilket du väljer. Se följande tabell för att få hjälp med ditt beslut. Vi rekommenderar Microsoft. Azure. Service Bus SDK eftersom det är modernt, presterande och är plattforms oberoende. Dessutom stöder den AMQP över WebSockets och är en del av Azure .NET SDK-samlingen med projekt med öppen källkod.

| NuGet-paket | Primär namnrymd (er) | Minsta plattform (er) | Protokoll |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft. Azure. Service Bus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Universell Windows-plattform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure. Service Bus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Mer information om lägsta stöd för .NET standard Platform finns i [.net implementation support](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Återanvända fabriker och klienter

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

Service Bus klient objekt, till exempel implementeringar av [`IQueueClient`][QueueClient] eller [`IMessageSender`][MessageSender] , ska registreras för beroende inmatning som singleton (eller instansieras en gång och delad). Vi rekommenderar att du inte stänger meddelande fabriker eller kö-, ämnes-och prenumerations klienter när du har skickat ett meddelande och sedan skapar det igen när du skickar nästa meddelande. Om du stänger en meddelande fabrik tas anslutningen till Service Bus-tjänsten bort och en ny anslutning upprättas när fabriken skapas på nytt. Att upprätta en anslutning är en dyr åtgärd som du kan undvika genom att återanvända samma fabriks-och klient objekt för flera åtgärder. Du kan använda dessa klient objekt på ett säkert sätt för samtidiga asynkrona åtgärder och från flera trådar.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

Service Bus klient objekt, till exempel `QueueClient` eller `MessageSender` , skapas via ett [MessagingFactory][MessagingFactory] -objekt, vilket även ger intern hantering av anslutningar. Vi rekommenderar att du inte stänger meddelande fabriker eller kö-, ämnes-och prenumerations klienter när du har skickat ett meddelande och sedan skapar det igen när du skickar nästa meddelande. Om du stänger en meddelande fabrik tas anslutningen till Service Bus-tjänsten bort och en ny anslutning upprättas när fabriken skapas på nytt. Att upprätta en anslutning är en dyr åtgärd som du kan undvika genom att återanvända samma fabriks-och klient objekt för flera åtgärder. Du kan använda dessa klient objekt på ett säkert sätt för samtidiga asynkrona åtgärder och från flera trådar.

---

## <a name="concurrent-operations"></a>Samtidiga åtgärder

Det tar lite tid att utföra en åtgärd (skicka, ta emot, ta bort osv.). Den här gången inkluderar bearbetningen av åtgärden av Service Bus tjänsten, förutom svars tiden för begäran och svaret. För att öka antalet åtgärder per tid måste åtgärder köras samtidigt.

Klienten schemalägger samtidiga åtgärder genom att utföra asynkrona åtgärder. Nästa begäran startas innan den tidigare begäran har slutförts. Följande kodfragment är ett exempel på en asynkron sändnings åtgärd:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

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

Följande kod är ett exempel på en asynkron mottagnings åtgärd.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

Se GitHub-lagringsplatsen för fullständiga <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">käll kod <span class="docon docon-navigate-external x-hidden-focus"></span> exempel </a>:

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
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`Objektet instansieras med anslutnings strängen, könamnet och ett gransknings se mottagnings läge. Sedan `receiver` används instansen för att registrera meddelande hanteraren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

Se GitHub-lagringsplatsen för fullständiga <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">käll kod <span class="docon docon-navigate-external x-hidden-focus"></span> exempel </a>:

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
        MaxConcurrentCalls = 20
    });
```

`MessagingFactory`Skapar ett `factory` objekt från anslutnings strängen. Med `factory` instansen `MessageReceiver` instansieras en. Sedan `receiver` används instansen för att registrera meddelande hanteraren.

---

## <a name="receive-mode"></a>Mottagnings läge

När du skapar en kö eller en prenumerations klient kan du ange ett mottagnings läge: *Peek-lock* eller *ta emot och ta bort*. Förvalt mottagnings läge är `PeekLock` . Vid användning i standard läget skickar klienten en begäran om att ta emot ett meddelande från Service Bus. När klienten har tagit emot meddelandet skickar den en förfrågan om att slutföra meddelandet.

När du ställer in mottagnings läget till `ReceiveAndDelete` kombineras båda stegen i en enskild begäran. De här stegen minskar det övergripande antalet åtgärder och kan förbättra det övergripande meddelande data flödet. Den här prestandan ger risk för att förlora meddelanden.

Service Bus stöder inte transaktioner för Receive-och-Delete-åtgärder. Dessutom krävs Peek-lock-semantik för alla scenarier där klienten vill skjuta upp eller [döda](service-bus-dead-letter-queues.md) ett meddelande.

## <a name="client-side-batching"></a>Batchbearbetning på klient Sidan

Med batching på klient sidan kan en kö-eller ämnes klient fördröja sändningen av ett meddelande under en viss tids period. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. Satsvis batchbearbetning på klient sidan gör att en kö eller prenumerations klient kan **utföra flera fullständiga** förfrågningar till en enda begäran. Batching är endast tillgängligt för asynkrona åtgärder för att **Skicka** och **slutföra** . Synkrona åtgärder skickas omedelbart till Service Bus tjänsten. Satsvis kompilering sker inte för gransknings-eller mottagnings åtgärder, eller så sker batching mellan klienter.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

Batching-funktionen för .NET standard SDK, visar ännu inte en egenskap för att manipulera.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

Som standard använder en klient ett batch-intervall på 20 MS. Du kan ändra batch-intervallet genom att ange egenskapen [BatchFlushInterval][BatchFlushInterval] innan du skapar meddelande fabriken. Den här inställningen påverkar alla klienter som skapas av den här fabriken.

Om du vill inaktivera batchbearbetning ställer du in egenskapen [BatchFlushInterval][BatchFlushInterval] på **TimeSpan. Zero**. Ett exempel:

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

Batching påverkar inte antalet fakturerbara meddelande åtgärder och är bara tillgängligt för Service Bus klient protokoll med hjälp av [Microsoft. Service Bus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -biblioteket. HTTP-protokollet stöder inte batchbearbetning.

> [!NOTE]
> Inställningen `BatchFlushInterval` säkerställer att batchen är implicit från programmets perspektiv. dvs. programmet gör `SendAsync` och `CompleteAsync` anropar och utför inga specifika batch-anrop.
>
> Explicit batchbearbetning på klient sidan kan implementeras med hjälp av metod anropet nedan:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Här är den kombinerade storleken på meddelandena som måste vara mindre än den maximala storlek som stöds av pris nivån.

---

## <a name="batching-store-access"></a>Åtkomst till grupp lagring

För att öka data flödet för en kö, ett ämne eller en prenumeration Service Bus batchar flera meddelanden när de skrivs till den interna butiken. Om den aktive ras i en kö eller ett ämne, kommer att skriva meddelanden i lagret. Om det är aktiverat i en kö eller prenumeration, kommer borttagning av meddelanden från arkivet att grupperas. Om grupplagrad lagrings åtkomst har Aktiver ATS för en entitet kan Service Bus fördröja en lagrings Skriv åtgärd för den entiteten med upp till 20 MS.

> [!NOTE]
> Det finns ingen risk för förlust av meddelanden med batchering, även om det uppstår Service Bus ett problem i slutet av ett 20ms batching-intervall.

Ytterligare lagrings åtgärder som utförs under det här intervallet läggs till i batchen. Grupp åtkomst påverkar endast åtgärder för att **Skicka** och **slutföra** . Receive-åtgärder påverkas inte. Grupp åtkomst är en egenskap för en entitet. Satsvis kompilering sker över alla entiteter som aktiverar grupp åtkomst.

När du skapar en ny kö, ämne eller prenumeration, aktive ras grupp åtkomst som standard.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

Om du vill inaktivera grupp åtkomst måste du ha en instans av en `ManagementClient` . Skapa en kö från en beskrivning av kön som anger `EnableBatchedOperations` egenskapen till `false` .

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

Om du vill inaktivera grupp åtkomst måste du ha en instans av en `NamespaceManager` . Skapa en kö från en beskrivning av kön som anger `EnableBatchedOperations` egenskapen till `false` .

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

Åtkomst till grupp lagring påverkar inte antalet fakturerbara meddelande åtgärder och är en egenskap för en kö, ett ämne eller en prenumeration. Den är oberoende av mottagnings läget och det protokoll som används mellan en klient och tjänsten Service Bus.

## <a name="prefetching"></a>För hämtning

För [hämtning](service-bus-prefetch.md) gör att kön eller prenumerations klienten kan läsa in ytterligare meddelanden från tjänsten när en Receive-åtgärd utförs. Klienten lagrar dessa meddelanden i en lokal cache. Storleken på cachen bestäms av `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` egenskaperna eller. Varje klient som aktiverar för hämtning bevarar sin egen cache. Ett cacheminne delas inte mellan klienter. Om klienten initierar en mottagnings åtgärd och dess cacheminne är tom, skickar tjänsten en batch med meddelanden. Storleken på batchen är lika med storleken på cachen eller 256 KB, beroende på vilket som är mindre. Om klienten initierar en mottagnings åtgärd och cachen innehåller ett meddelande, hämtas meddelandet från cachen.

När ett meddelande förhämtas låser tjänsten det förhämtade meddelandet. Med låset kan det förhämtade meddelandet inte tas emot av en annan mottagare. Om mottagaren inte kan slutföra meddelandet innan låset förfaller blir meddelandet tillgängligt för andra mottagare. Den förhämtade kopian av meddelandet finns kvar i cacheminnet. Mottagaren som använder den förfallna cachelagrade kopian får ett undantag när den försöker slutföra meddelandet. Som standard upphör meddelande låset att gälla efter 60 sekunder. Det här värdet kan utökas till 5 minuter. För att förhindra att förfallna meddelanden används bör cachestorleken alltid vara mindre än antalet meddelanden som kan användas av en klient inom tids gränsen för låsning.

När du använder standard låsets förfallo tid på 60 sekunder är ett utmärkt värde för `PrefetchCount` 20 gånger den maximala bearbetnings takten för alla mottagare av fabriken. Till exempel skapar en fabrik tre mottagare och varje mottagare kan bearbeta upp till 10 meddelanden per sekund. För hämtnings antalet får inte överstiga 20 × 3 X 10 = 600. Som standard `PrefetchCount` är 0 inställt på 0, vilket innebär att inga ytterligare meddelanden hämtas från tjänsten.

För hämtning av meddelanden ökar det totala data flödet för en kö eller prenumeration eftersom det minskar det övergripande antalet meddelande åtgärder, eller tur och ingångar. Att hämta det första meddelandet tar dock längre tid (på grund av den ökade meddelande storleken). Det går snabbare att ta emot förhämtade meddelanden eftersom dessa meddelanden redan har hämtats av klienten.

Egenskapen TTL (Time-to-Live) för ett meddelande kontrol leras av servern vid den tidpunkt då servern skickar meddelandet till klienten. Klienten kontrollerar inte meddelandets TTL-egenskap när meddelandet tas emot. I stället kan meddelandet tas emot även om meddelandets TTL-värde har skickats medan meddelandet cachelagrades av klienten.

För hämtning påverkar inte antalet fakturerbara meddelande åtgärder och är bara tillgängligt för det Service Bus klient protokollet. HTTP-protokollet har inte stöd för för hämtning. För hämtning är tillgängligt för både synkrona och asynkrona mottagnings åtgärder.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

Mer information finns i följande `PrefetchCount` Egenskaper:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. Service Bus SDK](#tab/net-framework-sdk)

Mer information finns i följande `PrefetchCount` Egenskaper:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>För hämtning och ReceiveBatch

> [!NOTE]
> Det här avsnittet gäller endast för SDK för WindowsAzure. Service Bus, eftersom Microsoft. Azure. Service Bus SDK inte visar batch-funktioner.

Även om begreppen för att hämta flera meddelanden tillsammans har liknande semantik för att bearbeta meddelanden i en batch ( `ReceiveBatch` ), finns det vissa mindre skillnader som måste behållas i åtanke när de används tillsammans.

För hämtning är en konfiguration (eller läge) på klienten ( `QueueClient` och `SubscriptionClient` ) och `ReceiveBatch` är en åtgärd (som har semantik för begäran-svar).

Tänk på följande när du använder dem tillsammans:

* Prefetch ska vara större än eller lika med antalet meddelanden som du förväntar dig att ta emot från `ReceiveBatch` .
* För hämtning kan vara upp till n/3 gånger antalet behandlade meddelanden per sekund, där n är standard längden för låset.

Det finns vissa utmaningar med att ha en girig metod (dvs. behålla antalet för hämtningar mycket högt), eftersom det innebär att meddelandet är låst till en viss mottagare. Rekommendationen är att testa för hämtnings värden mellan tröskelvärdena ovan och identifiera vad som passar.

## <a name="multiple-queues"></a>Flera köer

Om den förväntade inläsningen inte kan hanteras av en enskild kö eller ett ämne måste du använda flera meddelande enheter. När du använder flera entiteter skapar du en dedikerad klient för varje entitet, i stället för att använda samma klient för alla entiteter.

## <a name="development-and-testing-features"></a>Funktioner för utveckling och testning

> [!NOTE]
> Det här avsnittet gäller endast för SDK för WindowsAzure. Service Bus, eftersom den här funktionen inte exponeras av Microsoft. Azure. Service Bus SDK.

Service Bus har en funktion som används specifikt för utveckling och som **aldrig ska användas i produktions konfigurationerna**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

När nya regler eller filter läggs till i ämnet kan du använda [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] för att kontrol lera att det nya filter uttrycket fungerar som förväntat.

## <a name="scenarios"></a>Scenarier

I följande avsnitt beskrivs vanliga meddelande scenarier och en beskrivning av de önskade Service Bus inställningarna. Data flödes hastigheter klassificeras som små (mindre än 1 meddelande/sekund), måttligt (1 meddelande/sekund eller större men mindre än 100 meddelanden/sekund) och hög (100 meddelanden/sekund eller mer). Antalet klienter klassificeras som små (5 eller färre), måttligt (mer än 5 men mindre än eller lika med 20) och stor (mer än 20).

### <a name="high-throughput-queue"></a>Kö med hög data flöde

Mål: maximera data flödet i en enskild kö. Antalet avsändare och mottagare är litet.

* Du kan öka den totala sändnings hastigheten i kön genom att använda flera meddelande fabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Om du vill öka den totala mottagnings frekvensen från kön använder du flera meddelande fabriker för att skapa mottagare.
* Använd asynkrona åtgärder för att dra nytta av batching på klient sidan.
* Ange batch-intervallet till 50 ms för att minska antalet Service Bus klient protokoll överföring. Om flera avsändare används ökar du batch-intervallet till 100 MS.
* Lämna åtkomst till grupp lagring aktiverat. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas till kön.
* Ange antalet för hämtningar till 20 gånger den maximala bearbetnings takten för alla mottagare av en fabrik. Det här antalet minskar antalet Service Bus klient protokoll överföringar.

### <a name="multiple-high-throughput-queues"></a>Flera köer med hög data flöde

Mål: maximera det totala data flödet i flera köer. Data flödet för en enskild kö är måttligt eller högt.

Om du vill få maximalt data flöde i flera köer använder du inställningarna som beskrivs för att maximera genomflödet i en enskild kö. Använd dessutom olika fabriker för att skapa klienter som skickar eller tar emot från olika köer.

### <a name="low-latency-queue"></a>Kö för låg latens

Mål: minimera svars tid från slut punkt till slut punkt för en kö eller ett ämne. Antalet avsändare och mottagare är litet. Data flödet för kön är litet eller måttligt.

* Inaktivera batchbearbetning på klient sidan. Klienten skickar omedelbart ett meddelande.
* Inaktivera grupplagrad lagrings åtkomst. Tjänsten skriver direkt meddelandet till arkivet.
* Om du använder en enda klient anger du för hämtnings antalet till 20 gånger bearbetnings takten för mottagaren. Om flera meddelanden anländer till kön samtidigt, skickar det Service Bus klient protokollet alla på samma tidpunkt. När klienten får nästa meddelande finns meddelandet redan i det lokala cacheminnet. Cachen måste vara liten.
* Om du använder flera klienter anger du för hämtnings antalet till 0. Genom att ange antalet kan den andra klienten ta emot det andra meddelandet medan den första klienten fortfarande bearbetar det första meddelandet.

### <a name="queue-with-a-large-number-of-senders"></a>Kö med ett stort antal avsändare

Mål: maximera data flödet för en kö eller ett ämne med ett stort antal avsändare. Varje avsändare skickar meddelanden med en måttlig taxa. Antalet mottagare är litet.

Service Bus möjliggör upp till 1000 samtidiga anslutningar till en meddelande enhet. Den här gränsen tillämpas på namn områdes nivå, och köer/ämnen/prenumerationer är begränsade av gränsen för samtidiga anslutningar per namnrymd. För köer delas numret mellan avsändare och mottagare. Om alla 1000 anslutningar krävs för avsändare ersätter du kön med ett ämne och en enda prenumeration. Ett ämne accepterar upp till 1000 samtidiga anslutningar från avsändare, medan prenumerationen godkänner ytterligare 1000 samtidiga anslutningar från mottagare. Om det krävs fler än 1000 samtidiga avsändare skickas meddelanden till Service Bus protokollet via HTTP.

Gör så här för att maximera data flödet:

* Om varje avsändare finns i en annan process ska du bara använda en enda fabrik per process.
* Använd asynkrona åtgärder för att dra nytta av batching på klient sidan.
* Använd standardvärdet för batching av 20 MS för att minska antalet Service Bus klient protokoll överföring.
* Lämna åtkomst till grupp lagring aktiverat. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas till kön eller ämnet.
* Ange antalet för hämtningar till 20 gånger den maximala bearbetnings takten för alla mottagare av en fabrik. Det här antalet minskar antalet Service Bus klient protokoll överföringar.

### <a name="queue-with-a-large-number-of-receivers"></a>Kö med ett stort antal mottagare

Mål: maximera mottagnings frekvensen för en kö eller prenumeration med ett stort antal mottagare. Varje mottagare tar emot meddelanden med en måttlig taxa. Antalet avsändare är litet.

Service Bus möjliggör upp till 1000 samtidiga anslutningar till en entitet. Om en kö kräver mer än 1000 mottagare, ersätter du kön med ett ämne och flera prenumerationer. Varje prenumeration har stöd för upp till 1000 samtidiga anslutningar. Du kan också få åtkomst till kön via HTTP-protokollet.

Maximera data flödet genom att göra följande:

* Om varje mottagare finns i en annan process ska du bara använda en enda fabrik per process.
* Mottagare kan använda synkrona eller asynkrona åtgärder. Med hänsyn till den måttliga mottagnings hastigheten för en enskild mottagare påverkar klient sidans batching av en fullständig begäran inte mottagarens data flöde.
* Lämna åtkomst till grupp lagring aktiverat. Den här åtkomsten minskar den övergripande belastningen på entiteten. Det minskar också den totala hastigheten med vilken meddelanden kan skrivas till kön eller ämnet.
* Ange ett litet värde för för hämtnings antalet (till exempel PrefetchCount = 10). Det här antalet förhindrar att mottagare inaktive ras medan andra mottagare har ett stort antal meddelanden i cacheminnet.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Ämne med ett litet antal prenumerationer

Mål: maximera data flödet för ett ämne med ett litet antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade mottagnings hastigheten för alla prenumerationer är större än sändnings takten. Antalet avsändare är litet. Antalet mottagare per prenumeration är litet.

Maximera data flödet genom att göra följande:

* Om du vill öka den totala sändnings hastigheten i avsnittet använder du flera meddelande fabriker för att skapa avsändare. Använd asynkrona åtgärder eller flera trådar för varje avsändare.
* Du kan öka den totala mottagnings hastigheten från en prenumeration genom att använda flera meddelande fabriker för att skapa mottagare. Använd asynkrona åtgärder eller flera trådar för varje mottagare.
* Använd asynkrona åtgärder för att dra nytta av batching på klient sidan.
* Använd standardvärdet för batching av 20 MS för att minska antalet Service Bus klient protokoll överföring.
* Lämna åtkomst till grupp lagring aktiverat. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas i avsnittet.
* Ange antalet för hämtningar till 20 gånger den maximala bearbetnings takten för alla mottagare av en fabrik. Det här antalet minskar antalet Service Bus klient protokoll överföringar.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Ämne med ett stort antal prenumerationer

Mål: maximera data flödet för ett ämne med ett stort antal prenumerationer. Ett meddelande tas emot av många prenumerationer, vilket innebär att den kombinerade mottagnings hastigheten för alla prenumerationer är mycket större än sändnings takten. Antalet avsändare är litet. Antalet mottagare per prenumeration är litet.

Ämnen med ett stort antal prenumerationer utökar vanligt vis ett lågt data flöde om alla meddelanden dirigeras till alla prenumerationer. Detta låga data flöde orsakas av det faktum att varje meddelande tas emot flera gånger och att alla meddelanden som finns i ett ämne och alla prenumerationer lagras i samma butik. Det förutsätts att antalet avsändare och antalet mottagare per prenumeration är litet. Service Bus stöder upp till 2 000 prenumerationer per ämne.

Prova följande steg för att maximera data flödet:

* Använd asynkrona åtgärder för att dra nytta av batching på klient sidan.
* Använd standardvärdet för batching av 20 MS för att minska antalet Service Bus klient protokoll överföring.
* Lämna åtkomst till grupp lagring aktiverat. Den här åtkomsten ökar den totala hastigheten med vilken meddelanden kan skrivas i avsnittet.
* Ange antalet för hämtningar till 20 gånger den förväntade mottagnings frekvensen i sekunder. Det här antalet minskar antalet Service Bus klient protokoll överföringar.

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