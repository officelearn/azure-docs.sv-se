---
title: Förhandsversionsfunktioner för varaktiga funktioner – Azure Functions
description: Läs mer om förhandsfunktionerna för varaktiga funktioner.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3b49049ea1ed36a08fad9619183017b0f07d99
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077747"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Varaktiga funktioner 2.0 preview (Azure Functions)

*Varaktiga funktioner* är en utökning av [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som hjälper dig att skriva tillståndskänsliga funktioner i en serverfri miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med varaktiga funktioner, finns i den [översikt dokumentation](durable-functions-overview.md).

Varaktiga funktioner är en allmän tillgänglighet (allmänt tillgänglig)-funktion i Azure Functions, men innehåller även flera underfunktioner som finns i offentlig förhandsversion. Den här artikeln beskriver nyligen utgivna funktioner och innehåller information om hur de fungerar och hur du kan börja använda dem.

> [!NOTE]
> De här förhandsgranskningsfunktionerna är en del av en beständig Functions 2.0-version som för närvarande är en **alpha högkvalitativa utgåvan** med flera ändringar. I Azure Functions varaktiga tilläggspaket bygger finns på nuget.org med versioner i form av **2.0.0-alpha**. Dessa versioner är inte lämplig för alla produktionsarbetsbelastningar och senare versioner kan innehålla ytterligare ändringar.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Flera ändringar har införts i varaktiga Functions 2.0. Befintliga program som inte förväntas vara kompatibel med varaktiga Functions 2.0 utan att kodändringar krävs. Det här avsnittet innehåller några av ändringarna:

### <a name="dropping-net-framework-support"></a>Släppa stöd för .NET Framework

Stöd för .NET Framework (och därför Functions 1.0) har släppts för varaktiga funktioner 2.0. Den främsta orsaken är att aktivera icke-Windows-deltagare att enkelt skapa och testa ändringar de gör i varaktiga funktioner från macOS och Linux-plattformar. Sekundär orsak är att hjälpa rekommenderar att utvecklare kan flytta till den senaste versionen av Azure Functions-körningen.

### <a name="hostjson-schema"></a>Host.json schema

Följande utdrag visar det nya schemat för host.json. Viktigaste ändringen känna till oss nya `"storageProvider"` avsnittet och `"azureStorage"` avsnittet under den. Den här ändringen gjordes för att stödja [alternativ lagringsprovidrar](durable-functions-preview.md#alternate-storage-providers).

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
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

Följande kod är ett exempel på en enkel enhet-funktion som definierar en *räknaren* entitet. Funktionen definierar tre åtgärder, `add`, `remove`, och `reset`, och var av som uppdaterar ett heltalsvärde `currentValue`.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityName = "Counter")] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entiteten *instanser* kan nås via en unik identifierare i *entitets-ID*. En entitets-ID är bara ett par med strängar som unikt identifierar en entitetsinstans. Det består av:

1. en **entitetsnamn**: ett namn som identifierar typ av entiteten (till exempel, ”räknaren”)
2. en **enhetsnyckel**: en sträng som unikt identifierar entiteten bland andra entiteter med samma namn (till exempel en GUID)

Till exempel en *räknaren* entitet funktionen kan användas för att hålla poäng i ett online-spel. Varje instans av spelet har en unik entitets-ID som `@Counter@Game1`, `@Counter@Game2`och så vidare.

### <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Utformningen av varaktiga entiteter kraftigt påverkas av den [aktörmodell](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med aktörer bör sedan koncepten bakom varaktiga entiteter känna till dig. I synnerhet varaktiga entiteter liknar [virtuella aktörer](https://research.microsoft.com/en-us/projects/orleans/) på många sätt:

* Hållbar entiteter är adresserbara via en *entitets-ID*.
* Hållbar entitet operations köra seriellt, en i taget för att förhindra konkurrenstillstånd.
* Hållbar entiteter skapas automatiskt när de kallas eller signalerade.
* När åtgärder utförs inte, tas hållbar entiteter tyst bort från minnet.

Det finns några viktiga skillnader, men som är värt:

* Hållbar entiteter modelleras som ren funktioner. Den här designen skiljer sig från de flesta objektorienterad ramverk som representerar aktörer använda språkspecifika stöd för klasser, egenskaper och metoder.
* Hållbar entiteter prioritera *hållbarhet* över *svarstid*, och därför inte lämplig för program med strikta svarstidskrav.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i ordning.
* Hållbar entiteter kan användas tillsammans med varaktiga orkestreringar och kan fungera som distribuerade lås, vilket beskrivs senare i den här artikeln.
* Begäran/svar-mönster i entiteter är begränsade till orkestreringar. För entiteten till entiteten kommunikation tillåts endast enkelriktade meddelanden (även kallat ”signalering”) som i den ursprungliga aktörmodell. Den här funktionen förhindrar distribuerade låsningar.

### <a name="durable-entity-apis"></a>Hållbar entitet API: er

Entitet-support omfattar flera API: er. För en finns det ett nytt API för att definiera entitet funktion, enligt ovan, som anger vad som ska hända när en åtgärd har anropats på en entitet. Befintliga API: er för klienter och orkestreringar har också uppdaterats med nya funktioner för interaktion med entiteter.

### <a name="implementing-entity-operations"></a>Implementera åtgärder för entitet

Körningen av en åtgärd på en entitet kan anropa dessa medlemmar på context-objektet (`IDurableEntityContext` i .NET):

* **OperationName**: hämtar namnet på åtgärden.
* **GetInput\<T >**: hämtar indata för åtgärden.
* **GetState\<T >**: hämtar det aktuella tillståndet för entiteten.
* **SetState**: uppdaterar tillståndet för entiteten.
* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **Self**: hämtar ID för entiteten.
* **Returnera**: returnerar ett värde till klient- eller dirigeringslösning som kallas igen.
* **IsNewlyConstructed**: returnerar `true` om entiteten inte fanns före åtgärden.
* **DestructOnExit**: tar bort entiteten när åtgärden är klar.

Åtgärder begränsas mindre än orkestreringar:

* Åtgärder kan anropa extern i/o, med synkron eller asynkron API: er som (Vi rekommenderar att du använder asynkrona som).
* Åtgärder kan vara icke-deterministisk. Till exempel är det säkert att anropa `DateTime.UtcNow`, `Guid.NewGuid()` eller `new Random()`.

### <a name="accessing-entities-from-clients"></a>Åtkomst till entiteter från klienter

Hållbar entiteter kan anropas från vanliga funktioner via de `orchestrationClient` bindning (`IDurableOrchestrationClient` i .NET). Följande stöds:

* **ReadEntityStateAsync\<T >**: läser tillståndet för en entitet.
* **SignalEntityAsync**: skickar ett enkelriktat meddelande till en entitet och väntar tills den placeras i kö.

Dessa metoder prioritera prestanda över konsekvens: `ReadEntityStateAsync` kan returnera ett värde i inaktuella och `SignalEntityAsync` kan returnera innan åtgärden har slutförts. Anropa entiteter från orkestreringar (enligt beskrivningen nedan) är däremot starkt konsekventa.

### <a name="accessing-entities-from-orchestrations"></a>Åtkomst till entiteter från orkestreringar

Orkestreringar har åtkomst till entiteter med hjälp av context-objektet. De kan välja mellan enkelriktad kommunikation (utlöses och Glöm) och dubbelriktad kommunikation (begäranden och svar). De respektive metoderna är

* **SignalEntity**: skickar ett enkelriktat meddelande till en entitet.
* **CallEntityAsync**: skickar ett meddelande till en entitet och väntar på ett svar som anger att åtgärden har slutförts.
* **CallEntityAsync\<T >**: skickar ett meddelande till en entitet och väntar på svar som innehåller ett resultat av typen T.

När du använder dubbelriktad kommunikation är också alla undantag vid körning av åtgärden skickas tillbaka till anropande orchestration och rethrown. När du använder fire-and-forget observerats däremot inte undantag.

### <a name="locking-entities-from-orchestrations"></a>Låsning entiteter från orkestreringar

Orkestreringar kan låsa enheter. Den här funktionen gör det enkelt att förhindra oönskade förenkling med hjälp av *viktiga avsnitt*.

Context-objektet innehåller följande metoder:

* **LockAsync**: Hämtar lås på en eller flera entiteter.
* **Är låst**: returnerar true om för närvarande i en kritisk sektion false annars.

Avsnittet kritiska avslutas och alla låsen släpps, när orchestration slutar. I .NET, `LockAsync` returnerar en `IDisposable` som avslutar viktiga avsnitt när du har tagits bort, som kan användas tillsammans med en `using` -satsen för att få en syntaktiska representation av avsnittet kritiska.

Till exempel överväga en orkestrering som krävs för att testa om det finns två spelare och tilldela dem båda till ett spel. Den här uppgiften kan implementeras med hjälp av ett kritiskt avsnitt på följande sätt:

```csharp

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
> Redis-providern är för närvarande experimentellt och endast stöd för funktionsappar som körs på en enda nod.