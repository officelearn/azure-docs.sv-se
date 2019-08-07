---
title: Durable Functions för hands versions funktioner – Azure Functions
description: Lär dig mer om förhands gransknings funktioner för Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 1609931cd5fcab0977ff64f680fbb1f253f3caaf
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782178"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 för hands version (Azure Functions)

*Durable Functions* är ett tillägg till [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som låter dig skriva tillstånds känsliga funktioner i en miljö utan server. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med Durable Functions kan du läsa mer i [översikts dokumentationen](durable-functions-overview.md).

Durable Functions 1. x är en GA (allmänt tillgänglig) funktion i Azure Functions, men innehåller också flera underfunktioner som för närvarande finns i en offentlig för hands version. I den här artikeln beskrivs nyligen utgivna för hands versions funktioner och information om hur de fungerar och hur du kan börja använda dem.

> [!NOTE]
> Dessa förhands gransknings funktioner ingår i en Durable Functions 2,0-version, som för närvarande är en för hands versions **kvalitet** med flera större ändringar. Azure Functions beständiga tilläggs paket versioner finns på nuget.org med versioner i form av **2.0.0-betaX**. Dessa versioner är inte avsedda för produktions arbets belastningar och senare versioner kan innehålla ytterligare avbrytande ändringar.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Flera brytande ändringar introduceras i Durable Functions 2,0. Befintliga program förväntas inte vara kompatibla med Durable Functions 2,0 utan kod ändringar. I det här avsnittet visas några av ändringarna:

### <a name="hostjson-schema"></a>Host. JSON-schema

Följande fragment visar det nya schemat för Host. JSON. De viktigaste ändringarna som är medvetna om är de nya underavsnitten:

* `"storageProvider"`(och `"azureStorage"` underavsnittet) för Storage-speciell konfiguration
* `"tracking"`för spårnings-och loggnings konfiguration
* `"notifications"`(och `"eventGrid"` underavsnittet) för konfiguration av event Grid-meddelanden

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Eftersom Durable Functions 2,0 fortsätter att stabiliseras kommer fler ändringar att införas i `durableTask` avsnittet Host. JSON. Mer information om dessa ändringar finns i [det här GitHub-problemet](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Offentliga gränssnitts ändringar

De olika kontext objekt som stöds av Durable Functions hade abstrakta bas klasser avsedda att användas vid enhets testning. Som en del av Durable Functions 2,0 har dessa abstrakta bas klasser bytts ut mot gränssnitt. Funktions kod som använder konkreta typer direkt påverkas inte.

Följande tabell representerar huvud ändringarna:

| Gammal typ | Ny typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Om en abstrakt basklass innehåller virtuella metoder har de här virtuella metoderna ersatts av tilläggs metoder som definierats i `DurableContextExtensions`.

## <a name="entity-functions"></a>Enhets funktioner

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd,så kallade varaktiga entiteter. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskildutlösnings typ, enhets utlösare. Till skillnad från Orchestrator-funktioner har enhets funktioner inga speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

### <a name="net-programing-models"></a>.NET-program modeller

Det finns två valfria programmerings modeller för att redigera varaktiga entiteter. Följande kod är ett exempel på en enkel *Counter* -entitet som implementeras som en standard funktion. Den här funktionen definierartre åtgärder `add`, `reset`, och `get`, som körs på ett heltals tillstånds värde `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Den här modellen fungerar bäst för enkla enhets implementeringar eller implementeringar som har en dynamisk uppsättning åtgärder. Det finns dock en klass-baserad programmerings modell som är användbar för entiteter som är statiska men har mer komplexa implementeringar. Följande exempel är en motsvarande implementering av `Counter` entiteten med hjälp av .NET-klasser och-metoder.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Den klassbaserade modellen liknar programmerings modellen som är populär av [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). I den här modellen definieras en entitetstyp som en .NET-klass. Varje metod i klassen är en åtgärd som kan anropas av en extern klient. Till skillnad från Orleans är .NET-gränssnitt valfria. I föregående *räknar* exempel används inget gränssnitt, men det kan fortfarande anropas via andra funktioner eller via HTTP API-anrop.

Enhets *instanser* nås via en unik identifierare, ENTITETS *-ID*. Ett entitets-ID är bara ett par med strängar som unikt identifierar en enhets instans. Det består av:

* Ett **entitetsnamn**: ett namn som identifierar entitetens typ (till exempel "Counter").
* En **enhets nyckel**: en sträng som unikt identifierar entiteten bland alla andra entiteter av samma namn (till exempel ett GUID).

En *Counter* -enhets funktion kan till exempel användas för att hålla poängen i ett onlinespel. Varje instans av spelet har ett unikt entitets-ID, t. `@Counter@Game1`ex `@Counter@Game2`., och så vidare.

### <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Designen av varaktiga enheter påverkas kraftigt av aktörs [modellen](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med aktörerna, bör begreppen bakom varaktiga entiteter vara bekanta med dig. I synnerhet är varaktiga entiteter snarlika [virtuella aktörer](https://research.microsoft.com/projects/orleans/) på många sätt:

* Varaktiga entiteter kan adresseras via ett *entitets-ID*.
* Varaktiga enhets åtgärder körs seriellt, en i taget, för att förhindra tävlings förhållanden.
* Varaktiga entiteter skapas automatiskt när de anropas eller signaleras.
* När åtgärder inte körs inaktive ras varaktiga entiteter tyst från minnet.

Det finns dock några viktiga skillnader, men det är värt att notera:

* Varaktiga enheter prioriterar *hållbarhet* över *svars tider*och kan därför vara lämpligt för program med strikt latens krav.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i rätt ordning.
* Varaktiga entiteter kan användas tillsammans med varaktiga dirigeringar och kan fungera som distribuerade lås, som beskrivs längre fram i den här artikeln.
* Mönster för begäran/svar i entiteter är begränsade till dirigering. För kommunikation mellan enheter och entiteter tillåts endast enkelriktade meddelanden (även kallade "signalering"), som i den ursprungliga aktörs modellen. Det här beteendet förhindrar distribuerade död lägen.

### <a name="durable-entity-net-apis"></a>.NET API: er för varaktig entitet

Support för entiteter omfattar flera API: er. Det finns ett nytt API för att definiera enhets funktioner, som du ser ovan, som anger vad som ska hända när en åtgärd anropas på en entitet. Dessutom har befintliga API: er för klienter och dirigering uppdaterats med nya funktioner för interaktion med entiteter.

#### <a name="implementing-entity-operations"></a>Implementera entitets-åtgärder

Körningen av en åtgärd på en entitet kan anropa dessa medlemmar på objektet context (`IDurableEntityContext` i .net):

* **OperationName**: hämtar namnet på åtgärden.
* **GetInput\<TInput >** : hämtar InInformationen för åtgärden.
* **GetState\<TState >** : hämtar entitetens aktuella status.
* **SetState**: uppdaterar enhetens status.
* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **Self**: hämtar ID för entiteten.
* **Return**: returnerar ett värde till klienten eller dirigeringen som anropade åtgärden.
* **IsNewlyConstructed**: returnerar `true` om entiteten inte fanns före åtgärden.
* **DestructOnExit**: tar bort entiteten när åtgärden har slutförts.

Åtgärder är mindre begränsade än Orchestration:

* Åtgärder kan anropa externa I/O med hjälp av synkrona eller asynkrona API: er (vi rekommenderar att endast använda asynkrona).
* Åtgärder kan vara icke-deterministiska. Det kan till exempel vara säkert att anropa `DateTime.UtcNow` `Guid.NewGuid()` eller `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Åtkomst till entiteter från klienter

Varaktiga entiteter kan anropas från vanliga funktioner via `orchestrationClient` bindningen`IDurableOrchestrationClient` (i .net). Följande metoder stöds:

* **ReadEntityStateAsync\<T >** : läser status för en entitet.
* **SignalEntityAsync**: skickar ett envägs meddelande till en entitet och väntar på att det ska placeras i kö.
* **SignalEntityAsync\<T >** : samma som `SignalEntityAsync` men använder ett genererat proxyobjekt av typen `T`.

Det tidigare `SignalEntityAsync` anropet kräver att du anger namnet på enhets åtgärden `string` som en och nytto lasten för åtgärden `object`som en. Följande exempel kod är ett exempel på det här mönstret:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Det är också möjligt att generera ett proxyobjekt för typ säker åtkomst. Om du vill generera en typ säker proxy måste entitetstypen implementera ett gränssnitt. Anta till exempel att `Counter` entiteten ovan implementerade ett `ICounter` gränssnitt, som definieras enligt följande:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klient koden kan sedan använda `SignalEntityAsync<T>` och `ICounter` ange gränssnittet som typ parameter för att generera en typ säker proxy. Användningen av typ säkra proxyservrar visas i följande kod exempel:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

I föregående exempel `proxy` är parametern en dynamiskt genererad instans av `ICounter`, som internt översätter anropet till `Add` till motsvarande (inte avskrivit) anrop till `SignalEntityAsync`.

Typ parametern för `SignalEntityAsync<T>` har följande begränsningar:

* Typ parametern måste vara ett gränssnitt.
* Det går bara att definiera metoder i gränssnittet. Egenskaper stöds inte.
* Varje metod måste definiera antingen en eller inga parametrar.
* Varje metod måste returnera antingen `void`, `Task`eller `Task<T>` där `T` är en JSON-serializeable typ.
* Gränssnittet måste implementeras av exakt en typ inom gränssnittets sammansättning.

I de flesta fall leder gränssnitt som inte uppfyller dessa krav på ett körnings undantag.

> [!NOTE]
> Det är viktigt att notera att metoderna `ReadEntityStateAsync` och `SignalEntityAsync` för `IDurableOrchestrationClient` att prioritera prestanda över konsekvens. `ReadEntityStateAsync`kan returnera ett inaktuellt värde `SignalEntityAsync` och kan returneras innan åtgärden har slutförts.

#### <a name="accessing-entities-from-orchestrations"></a>Åtkomst till entiteter från dirigering

Dirigeringar har åtkomst till entiteter `IDurableOrchestrationContext` med hjälp av objektet. De kan välja mellan enkelriktad kommunikation (eld och glömma) och tvåvägs kommunikation (begäran och svar). Respektive metod är:

* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **CallEntityAsync**: skickar ett meddelande till en entitet och väntar på ett svar som anger att åtgärden har slutförts.
* **CallEntityAsync\<T >** : skickar ett meddelande till en entitet och väntar på ett svar som innehåller ett resultat av typen T.

När du använder tvåvägs kommunikation överförs eventuella undantag som har utlösts under körningen av åtgärden tillbaka till den anropande dirigeringen och återkastas. Undantag observeras dock inte när du använder Fire-och-glömma.

För typ säker åtkomst kan Orchestration-funktioner generera proxyservrar baserat på ett gränssnitt. `CreateEntityProxy` Tilläggs metoden kan användas för detta ändamål:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

I det tidigare exemplet antogs en "Counter"-entitet som implementerade `IAsyncCounter` gränssnittet. Dirigeringen kunde sedan använda `IAsyncCounter` typ definitionen för att generera en proxytyp för synkron interaktion med entiteten.

### <a name="locking-entities-from-orchestrations"></a>Låsa entiteter från Orchestrations

Dirigeringar kan låsa entiteter. Den här funktionen är ett enkelt sätt att förhindra oönskade races med hjälp av *kritiska avsnitt*.

Objektet context innehåller följande metoder:

* **LockAsync**: hämtar lås på en eller flera entiteter.
* **IsLocked**: returnerar true om det finns i ett kritiskt avsnitt, annars FALSE.

Det kritiska avsnittet slutar och alla Lås släpps när dirigeringen upphör. I .net `LockAsync` returnerar ett `IDisposable` som avslutar det kritiska avsnittet när det tas bort, som kan användas tillsammans med en `using` sats för att få en syntaktisk representation av det kritiska avsnittet.

Anta till exempel att du måste testa om två spelare är tillgängliga och sedan tilldela dem till ett spel. Den här uppgiften kan implementeras med hjälp av ett kritiskt avsnitt enligt följande:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

I det kritiska avsnittet är båda spelarens entiteter låsta, vilket innebär att de inte kör andra åtgärder än de som anropas från det kritiska avsnittet). Det här beteendet förhindrar races med motstridiga åtgärder, till exempel att spelare tilldelas till ett annat spel eller att de loggar ut.

Vi tillämpar flera begränsningar för hur viktiga avsnitt kan användas. Dessa begränsningar är till för att förhindra död lägen och återinträde.

* Det går inte att kapsla kritiska avsnitt.
* Det går inte att skapa under dirigering med kritiska avsnitt.
* Kritiska avsnitt kan bara anropa entiteter som de har låst.
* Kritiska avsnitt kan inte anropa samma entitet med flera parallella anrop.
* Kritiska avsnitt kan endast signalera entiteter som de inte har låst.

## <a name="alternate-storage-providers"></a>Alternativa lagringsprovider

Det tåliga aktivitets ramverket har stöd för flera lagringsprovider idag, inklusive [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), en [intern emulator](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)och en experimentell [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -Provider. Men fram till nu stöds den varaktiga åtgärds utökningen för Azure Functions endast Azure Storage-providern. Från och med Durable Functions 2,0 läggs stöd för alternativa lagringsprovider till, från och med Redis-providern.

> [!NOTE]
> Durable Functions 2,0 stöder endast .NET standard 2,0-kompatibla providers. Vid tidpunkten för skrivning stöder Azure Service Bus-providern inte .NET standard 2,0 och är därför inte tillgänglig som en alternativ lagringsprovider.

### <a name="emulator"></a>Torn

[DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) -providern är ett lokalt minne, en icke-beständig lagringsprovider som lämpar sig för lokala testnings scenarier. Den kan konfigureras med följande minimala **värd. JSON** -schema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentell)

[DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) -providern behåller alla Dirigerings tillstånd till ett konfigurerat Redis-kluster.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Måste referera till namnet på en app-inställning eller en miljö variabel. Appens inställning eller miljö variabel ska innehålla ett Redis-anslutnings sträng värde i formatet *Server: port*. Till exempel `localhost:6379` för att ansluta till ett lokalt Redis-kluster.

> [!NOTE]
> Redis-providern är för närvarande experimentell och stöder bara Function-appar som körs på en enda nod. Det garanterar inte att Redis-leverantören någonsin kommer att göras allmänt tillgänglig och kan tas bort i en framtida version.
