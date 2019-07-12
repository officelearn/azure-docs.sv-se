---
title: Förhandsversionsfunktioner för varaktiga funktioner – Azure Functions
description: Läs mer om förhandsfunktionerna för varaktiga funktioner.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7101519aa4a87995dac3a7f11046eed84a2c09b6
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812763"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Varaktiga funktioner 2.0 preview (Azure Functions)

*Varaktiga funktioner* är en utökning av [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som hjälper dig att skriva tillståndskänsliga funktioner i en serverfri miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med varaktiga funktioner, finns i den [översikt dokumentation](durable-functions-overview.md).

Varaktiga funktioner 1.x är en allmän tillgänglighet (allmänt tillgänglig)-funktion i Azure Functions, men innehåller även flera underfunktioner som finns i offentlig förhandsversion. Den här artikeln beskriver nyligen utgivna funktioner och innehåller information om hur de fungerar och hur du kan börja använda dem.

> [!NOTE]
> De här förhandsgranskningsfunktionerna är en del av en beständig Functions 2.0-version som för närvarande är en **förhandsversionen kvalitet** med flera ändringar. I Azure Functions varaktiga tilläggspaket bygger finns på nuget.org med versioner i form av **2.0.0-betaX**. Dessa versioner är inte avsedda för arbetsbelastningar för produktion och senare versioner kan innehålla ytterligare ändringar.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Flera ändringar har införts i varaktiga Functions 2.0. Befintliga program som inte förväntas vara kompatibel med varaktiga Functions 2.0 utan att kodändringar krävs. Det här avsnittet innehåller några av ändringarna:

### <a name="hostjson-schema"></a>Host.json schema

Följande utdrag visar det nya schemat för host.json. Viktigaste ändringarna vara medvetna om är ny underavsnitt:

* `"storageProvider"` (och `"azureStorage"` underavsnittet) för storage-konfiguration
* `"tracking"` för spårning och loggningskonfiguration
* `"notifications"` (och `"eventGrid"` underavsnittet) för meddelandekonfigurationen för event grid

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

När varaktiga Functions 2.0 fortsätter att stabilisera, fler ändringar kommer att läggas till i `durableTask` avsnittet host.json. Mer information om dessa ändringar finns i [problemet GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Offentliga gränssnittet ändringar

De olika ”kontext”-objekt som stöds av varaktiga funktioner hade abstrakt basklasser som är avsedda att användas i Enhetstestning. Som en del av varaktiga funktioner 2.0 kan har dessa abstrakta basklasser ersatts med gränssnitt. Funktionskoden som använder konkreta direkt påverkas inte.

I följande tabell representerar viktigaste ändringarna:

| Gamla typ | Ny typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

I fall där virtuella metoder finns i en abstrakt basklass metoderna virtuella har ersatts av tilläggsmetoder som definierats i `DurableContextExtensions`.

## <a name="entity-functions"></a>Funktioner för entitet

Funktioner för entitet definierar åtgärder för att läsa och uppdatera små delar av tillstånd, kallas *varaktiga entiteter*. Som orchestrator-funktioner, funktioner för entiteten är funktioner med en särskild Utlösartyp *entitet utlösaren*. Till skillnad från orchestrator-funktioner har entiteten funktioner inte några begränsningar för specifik kod. Entiteten funktioner också hantera tillstånd uttryckligen i stället för implicit som representerar tillstånd via Kontrollflöde.

### <a name="net-programing-models"></a>Modeller för .NET-programmering

Det finns två valfria programmeringsmodeller för redigering av varaktiga entiteter. Följande kod är ett exempel på en enkel *räknaren* entitet som implementeras som en funktion som standard. Den här funktionen definierar tre *operations*, `add`, `reset`, och `get`, och var av som tillämpar ett heltalsvärde tillstånd, `currentValue`.

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

Den här modellen fungerar bäst för implementeringar av enkel enhet eller implementeringar som har en dynamisk uppsättning åtgärder. Men finns det också en MOF-baserade programmeringsmodell som är användbar för entiteter som är statisk men har mer komplexa implementeringar. I följande exempel är en motsvarande implementering av den `Counter` entitet med hjälp av .NET-klasser och metoder.

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

MOF-baserade modellen liknar programmeringsmiljö känt från [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). I den här modellen definieras en entitetstyp som en .NET-klass. Varje metod i klassen är en åtgärd som kan anropas av en extern klient. Till skillnad från Orleans, men är .NET-gränssnitt valfria. Den tidigare *räknaren* exempel inte har använt ett gränssnitt, men det kan fortfarande anropas via andra funktioner eller via HTTP API-anrop.

Entiteten *instanser* kan nås via en unik identifierare i *entitets-ID*. En entitets-ID är bara ett par med strängar som unikt identifierar en entitetsinstans. Det består av:

* En **entitetsnamn**: ett namn som identifierar typ av entiteten (till exempel, ”räknaren”).
* En **enhetsnyckel**: en sträng som unikt identifierar entiteten bland andra entiteter med samma namn (till exempel en GUID).

Till exempel en *räknaren* entitet funktionen kan användas för att hålla poäng i ett online-spel. Varje instans av spelet har en unik entitets-ID som `@Counter@Game1`, `@Counter@Game2`och så vidare.

### <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Utformningen av varaktiga entiteter kraftigt påverkas av den [aktörmodell](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med aktörer bör sedan koncepten bakom varaktiga entiteter känna till dig. I synnerhet varaktiga entiteter liknar [virtuella aktörer](https://research.microsoft.com/projects/orleans/) på många sätt:

* Hållbar entiteter är adresserbara via en *entitets-ID*.
* Hållbar entitet operations köra seriellt, en i taget för att förhindra konkurrenstillstånd.
* Hållbar entiteter skapas automatiskt när de kallas eller signalerade.
* När åtgärder utförs inte, tas hållbar entiteter tyst bort från minnet.

Det finns några viktiga skillnader, men som är värt:

* Hållbar entiteter prioritera *hållbarhet* över *svarstid*, och därför inte lämplig för program med strikta svarstidskrav.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i ordning.
* Hållbar entiteter kan användas tillsammans med varaktiga orkestreringar och kan fungera som distribuerade lås, vilket beskrivs senare i den här artikeln.
* Begäran/svar-mönster i entiteter är begränsade till orkestreringar. För entiteten till entiteten kommunikation tillåts endast enkelriktade meddelanden (även kallat ”signalering”) som i den ursprungliga aktörmodell. Den här funktionen förhindrar distribuerade låsningar.

### <a name="durable-entity-net-apis"></a>Hållbar entitet .NET API: er

Entitet-support omfattar flera API: er. För en finns det ett nytt API för att definiera entitet funktion, enligt ovan, som anger vad som ska hända när en åtgärd har anropats på en entitet. Befintliga API: er för klienter och orkestreringar har också uppdaterats med nya funktioner för interaktion med entiteter.

#### <a name="implementing-entity-operations"></a>Implementera åtgärder för entitet

Körningen av en åtgärd på en entitet kan anropa dessa medlemmar på context-objektet (`IDurableEntityContext` i .NET):

* **OperationName**: hämtar namnet på åtgärden.
* **GetInput\<tINSATSMATERIAL >** : hämtar indata för åtgärden.
* **GetState\<TState >** : hämtar det aktuella tillståndet för entiteten.
* **SetState**: uppdaterar tillståndet för entiteten.
* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **Self**: hämtar ID för entiteten.
* **Returnera**: returnerar ett värde till klient- eller dirigeringslösning som kallas igen.
* **IsNewlyConstructed**: returnerar `true` om entiteten inte fanns före åtgärden.
* **DestructOnExit**: tar bort entiteten när åtgärden är klar.

Åtgärder begränsas mindre än orkestreringar:

* Åtgärder kan anropa extern i/o, med synkron eller asynkron API: er som (Vi rekommenderar att du använder asynkrona som).
* Åtgärder kan vara icke-deterministisk. Till exempel är det säkert att anropa `DateTime.UtcNow`, `Guid.NewGuid()` eller `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Åtkomst till entiteter från klienter

Hållbar entiteter kan anropas från vanliga funktioner via de `orchestrationClient` bindning (`IDurableOrchestrationClient` i .NET). Följande stöds:

* **ReadEntityStateAsync\<T >** : läser tillståndet för en entitet.
* **SignalEntityAsync**: skickar ett enkelriktat meddelande till en entitet och väntar tills den placeras i kö.
* **SignalEntityAsync\<T >** : samma som `SignalEntityAsync` men använder en genererad proxy-objekt av typen `T`.

Den tidigare `SignalEntityAsync` anrop krävs att ange namnet på entiteten åtgärden eftersom en `string` och nyttolast för åtgärden som en `object`. Följande exempelkod är ett exempel på det här mönstret:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Det går också att generera ett proxyobjekt för typen safe åtkomst. Om du vill generera en typ-safe-proxy, måste entitetstypen implementera ett gränssnitt. Anta exempelvis att den `Counter` entitet som vi nämnde tidigare implementerat en `ICounter` gränssnitt, definieras enligt följande:

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

Klientkoden kan sedan använda `SignalEntityAsync<T>` och ange den `ICounter` gränssnitt som typparameter att generera en typ-safe-proxy. Den här användningen av typen safe proxyservrar visas i följande kodexempel:

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

I exemplet ovan den `proxy` parameter är ett dynamiskt genererat instans av `ICounter`, som internt översätter anropet till `Add` till motsvarande (frågans) anrop till `SignalEntityAsync`.

> [!NOTE]
> Det är viktigt att notera att den `ReadEntityStateAsync` och `SignalEntityAsync` metoderna i `IDurableOrchestrationClient` prioritera prestanda över konsekvens. `ReadEntityStateAsync` kan returnera ett värde i inaktuella och `SignalEntityAsync` kan returnera innan åtgärden har slutförts.

#### <a name="accessing-entities-from-orchestrations"></a>Åtkomst till entiteter från orkestreringar

Orkestreringar har åtkomst till entiteter med hjälp av den `IDurableOrchestrationContext` objekt. De kan välja mellan enkelriktad kommunikation (utlöses och Glöm) och dubbelriktad kommunikation (begäranden och svar). Respektive metoderna är:

* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **CallEntityAsync**: skickar ett meddelande till en entitet och väntar på ett svar som anger att åtgärden har slutförts.
* **CallEntityAsync\<T >** : skickar ett meddelande till en entitet och väntar på svar som innehåller ett resultat av typen T.

När du använder dubbelriktad kommunikation är också alla undantag vid körning av åtgärden skickas tillbaka till anropande orchestration och rethrown. När du använder fire-and-forget observerats däremot inte undantag.

Orchestration-funktioner kan generera proxyservrar baserat på ett gränssnitt för typen safe åtkomst. Den `CreateEntityProxy` tilläggsmetod kan användas för detta ändamål:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration)]
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

I exemplet ovan har en ”in”-entitet antas finnas som implementerar den `IAsyncCounter` gränssnitt. Dirigering kunde sedan använda den `IAsyncCounter` av typdefinitionen att generera en proxytyp för synkront interagerar med entiteten.

### <a name="locking-entities-from-orchestrations"></a>Låsning entiteter från orkestreringar

Orkestreringar kan låsa enheter. Den här funktionen gör det enkelt att förhindra oönskade förenkling med hjälp av *viktiga avsnitt*.

Context-objektet innehåller följande metoder:

* **LockAsync**: Hämtar lås på en eller flera entiteter.
* **Är låst**: returnerar true om för närvarande i en kritisk sektion false annars.

Avsnittet kritiska avslutas och alla låsen släpps, när orchestration slutar. I .NET, `LockAsync` returnerar en `IDisposable` som avslutar viktiga avsnitt när du har tagits bort, som kan användas tillsammans med en `using` -satsen för att få en syntaktiska representation av avsnittet kritiska.

Till exempel överväga en orkestrering som krävs för att testa om det finns två spelare och tilldela dem båda till ett spel. Den här uppgiften kan implementeras med hjälp av ett kritiskt avsnitt på följande sätt:

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

I avsnittet kritiska, är både player-entiteter låsta, vilket innebär att de inte kör åtgärder än de som anropas från inom det kritiska avsnittet). Detta förhindrar förenkling med motstridiga åtgärder, till exempel tilldelas till en annan spelare spel eller signering av.

Vi tillämpar flera begränsningar på hur viktiga avsnitt kan användas. Dessa begränsningar fungera att förhindra låsningar och återinträde.

* Viktiga avsnitt kan inte kapslas.
* Viktiga avsnitt kan inte skapa suborchestrations.
* Viktiga avsnitt kan anropa endast de enheter som de har låst.
* Viktiga avsnitt kan inte anropa samma entitet med hjälp av flera parallella anrop.
* Viktiga avsnitt kan signalera endast de enheter som de inte har låst.

## <a name="alternate-storage-providers"></a>Alternativa lagrings-providers

Hållbar uppgift ramverket har stöd för flera lagringsprovidrar idag, inklusive [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), en [InMemory-emulatorn](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator), och en experimentell [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) provider. Fram till nu stöds tillägget varaktiga uppgift för Azure Functions endast dock Azure Storage-providern. Från och med varaktiga funktioner 2.0, stöd för alternativa lagringsprovidrar läggs till, från och med Redis-providern.

> [!NOTE]
> Varaktiga funktioner 2.0 stöder endast .NET Standard 2.0-kompatibla leverantörer. Vid tidpunkten för skrivning, Azure Service Bus-providern har inte stöd för .NET Standard 2.0 och kan därför inte tillgänglig som en annan lagringsprovider.

### <a name="emulator"></a>Emulatorn

Den [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) providern är ett lokalt minne, icke-varaktiga lagringsprovidern lämplig för lokal testning av scenarier. Den kan konfigureras med hjälp av följande minimal **host.json** schema:

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

Den [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) providern kvarstår alla orchestration-tillstånd till ett konfigurerat Redis-kluster.

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

Den `connectionStringName` måste referera till namnet på en app-inställningen eller miljö variabel. App inställning eller miljö variabeln ska innehålla ett strängvärde för Redis-anslutning i form av *server: port*. Till exempel `localhost:6379` för att ansluta till ett lokalt Redis-kluster.

> [!NOTE]
> Redis-providern är för närvarande experimentellt och endast stöd för funktionsappar som körs på en enda nod. Det är inte säkert att Redis-providern någonsin görs allmänt tillgänglig den kan tas bort i kommande versioner.
