---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284391"
---
Fel som har Aktiver ATS i en Azure Functions kan komma från något av följande ursprung:

- Användning av inbyggda Azure Functions [utlösare och bindningar](..\articles\azure-functions\functions-triggers-bindings.md)
- Anrop till API: er för underliggande Azure-tjänster
- Anrop till REST-slutpunkter
- Anrop till klient bibliotek, paket eller API: er från tredje part

Följande metoder för bra fel hantering är viktigt för att undvika förlust av data eller missade meddelanden. Rekommenderade fel hanterings metoder omfattar följande åtgärder:

- [Aktivera Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Använd strukturerad fel hantering](#use-structured-error-handling)
- [Design för idempotens](../articles/azure-functions/functions-idempotent.md)
- [Implementera principer för återförsök](#retry-policies) (där det är lämpligt)

### <a name="use-structured-error-handling"></a>Använd strukturerad fel hantering

Det är viktigt att samla in och logga fel för att övervaka hälso tillståndet för programmet. Den översta nivån i en funktions kod ska innehålla ett try/catch-block. I catch-blocket kan du samla in och logga fel.

## <a name="retry-policies"></a>Principer för återförsök

En princip för återförsök kan definieras för alla funktioner för utlösnings typ i din Function-app.  Principen för återförsök kör om en funktion tills körningen lyckades eller tills det maximala antalet återförsök inträffar.  Principer för återförsök kan definieras för alla funktioner i en app eller för enskilda funktioner.  Som standard försöker en Function-app inte göra om meddelanden (förutom de [angivna utlösare som har en princip för återförsök på utlösnings källan](#trigger-specific-retry-support)).  En princip för återförsök utvärderas varje gång en körning resulterar i ett undantag som inte kan identifieras.  Som bästa praxis bör du fånga alla undantag i koden och återskapa eventuella fel som bör resultera i ett nytt försök.  Det går inte att skriva Event Hubs och Azure Cosmos DB kontroll punkter förrän återförsöks principen för körningen har slutförts, vilket innebär att förloppet på den partitionen pausas tills den aktuella batchen har slutförts.

### <a name="retry-policy-options"></a>Försök igen princip alternativ

Följande alternativ är tillgängliga för att definiera en princip för återförsök.

Maximalt antal **återförsök** är det maximala antalet gånger som en körning har gjorts om före eventuella fel. Ett värde på `-1` innebär att du vill försöka på obestämd tid. Det aktuella antalet återförsök lagras i instansens minne. Det är möjligt att en instans har ett fel mellan nya försök.  När en instans Miss lyckas under en princip för återförsök försvinner antalet återförsök.  Om det finns instans fel, utlösare som Event Hubs, Azure Cosmos DB och Queue Storage kan återuppta bearbetningen och försöka utföra batchen igen på en ny instans, med antalet nya försök återställt till noll.  Andra utlösare, t. ex. HTTP och timer, återupptas inte på en ny instans.  Det innebär att max antalet återförsök är bästa möjliga, och i vissa sällsynta fall kan en körning göras mer än det högsta, eller för utlösare som HTTP och timer-försök som är mindre än max värdet.

**Återförsöks strategi** styr hur återförsök fungerar.  Följande är två alternativ för omförsök som stöds:

| Alternativ | Beskrivning|
|---|---|
|**`fixedDelay`**| En angiven tids period får förflyta mellan varje nytt försök,|
| **`exponentialBackoff`**| Det första återförsöket väntar för minsta fördröjning. Vid efterföljande återförsök läggs tiden till exponentiellt till den inledande varaktigheten för varje nytt försök tills den maximala fördröjningen uppnås.  Exponentiella säkerhets kopieringar lägger till en liten slump mellan att fördröja nya försök i stora data flödes scenarier.|

#### <a name="app-level-configuration"></a>Konfiguration av app-nivå

En princip för återförsök kan definieras för alla funktioner i en app [med hjälp av `host.json` filen](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Konfiguration av funktions nivå

En princip för återförsök kan definieras för en viss funktion.  En funktions bestämd konfiguration prioriteras över konfiguration på program nivå.

#### <a name="fixed-delay-retry"></a>Försök till fast fördröjning

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är principen för återförsök i *function.jspå* filen:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Här är principen för återförsök i *function.jspå* filen:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Nytt försök för exponentiell backoff

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jspå egenskap  |Attributets egenskap | Beskrivning |
|---------|---------|---------| 
|strategi|saknas|Krävs. Återförsöksstrategin som ska användas. Giltiga värden är `fixedDelay` eller `exponentialBackoff` .|
|maxRetryCount|saknas|Krävs. Maximalt antal tillåtna försök per funktions körning. `-1` innebär ett nytt försök på obestämd tid.|
|delayInterval|saknas|Fördröjningen som ska användas mellan återförsök när du använder en `fixedDelay` strategi.|
|minimumInterval|saknas|Den minsta fördröjningen för återförsök när du använder `exponentialBackoff` strategin.|
|maximumInterval|saknas|Den maximala fördröjningen vid nya försök när du använder `exponentialBackoff` strategin.| 

## <a name="trigger-specific-retry-support"></a>Utlösare för att aktivera stöd för återförsök

Vissa utlösare ger nya försök till utlösarens källa.  Dessa utlösare återförsök kan användas förutom eller som ersättning för funktionens återförsöks princip för program värden.  Om ett fast antal nya försök önskas bör du använda den utlösade principen för återförsök för principen för allmän värd återförsök.  Följande utlösare stöder återförsök vid utlösarens Källa:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kö/ämne)](../articles/azure-functions/functions-bindings-service-bus.md)

Som standard begär de här utlösarna nya försök upp till fem gånger. Efter det femte försöket har både Azure Queue Storage och Azure Service Bus utlösts Skriv ett meddelande till en [Poison-kö](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
