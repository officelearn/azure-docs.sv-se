---
title: Diagnostik i Durable Functions – Azure
description: Lär dig hur du diagnostiserar problem med Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: d2badee3eaa5a9af48e89adc1b59beacc1571792
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933510"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostik i Durable Functions i Azure

Det finns flera alternativ för att diagnosticera problem med [Durable Functions](durable-functions-overview.md). Vissa av dessa alternativ är desamma för vanliga funktioner medan andra är unika för Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är det rekommenderade sättet att utföra diagnostik och övervakning i Azure Functions. Samma sak gäller för Durable Functions. En översikt över hur du utnyttjar Application Insights i din Function-app finns i [övervaka Azure Functions](../functions-monitoring.md).

Med Azure Functions tåligt tillägg kan du också spåra *händelser* som gör att du kan spåra slut punkt till slut punkt för ett dirigering. Dessa kan hittas och frågas med hjälp av [Application Insights Analytics](../../azure-monitor/app/analytics.md) -verktyget i Azure Portal.

### <a name="tracking-data"></a>Spårnings data

Varje livs cykel händelse i en Orchestration-instans gör att en spårnings händelse skrivs till **trace** -samlingen i Application Insights. Den här händelsen innehåller en **customDimensions** nytto last med flera fält.  Fält namn är alla anpassningsprefix med `prop__`.

* **hubName**: Namnet på den aktivitets hubb där dirigeringarna körs.
* **APPNAME**: Namnet på Function-appen. Detta är användbart när du har flera Function-appar som delar samma Application Insights-instans.
* **slotName**: [Distributions platsen](../functions-deployment-slots.md) där den aktuella Function-appen körs. Detta är användbart när du använder distributions platser för att få en version av dina dirigeringar.
* **functionname**: Namnet på Orchestrator-eller Activity-funktionen.
* **functionType**: Typ av funktion, till exempel **Orchestrator** eller **Activity**.
* **InstanceID**: Det unika ID: t för Orchestration-instansen.
* **tillstånd**: Instansens livs cykel körnings tillstånd. Giltiga värden är:
  * **Schemalagd**: Funktionen har schemalagts för körning men har ännu inte börjat köras.
  * **Startade**: Funktionen har börjat köras men har ännu inte förväntats eller slutförts.
  * **Förväntat**: Orchestrator har schemalagt arbete och väntar på att det ska slutföras.
  * **Lyssna**: Orchestrator lyssnar efter en extern händelse avisering.
  * **Slutfört**: Funktionen har slutförts.
  * **Misslyckades**: Funktionen misslyckades med ett fel.
* **orsak**: Ytterligare data som är associerade med spårnings händelsen. Om exempelvis en instans väntar på en extern händelse meddelande, anger det här fältet namnet på händelsen som väntar på. Om en funktion har misslyckats kommer detta att innehålla fel informationen.
* **isReplay**: Booleskt värde som anger om spårnings händelsen är för en upprepad körning.
* **extensionVersion**: Versionen av den varaktiga aktivitets utökningen. Detta är särskilt viktigt när du rapporterar möjliga buggar i tillägget. Tids krävande instanser kan rapportera flera versioner om en uppdatering inträffar när den körs.
* **sequenceNumber**: Körnings ordnings nummer för en händelse. Tillsammans med tidsstämpeln kan du beställa händelserna efter körnings tid. *Observera att det här antalet återställs till noll om värden startas om medan instansen körs, så det är viktigt att alltid sortera efter tidsstämpel först, sedan sequenceNumber.*

Utförligheten för spårnings data som skickas till Application Insights kan konfigureras i `logger` avsnittet (funktion 1. x) `logging` eller (funktion 2. `host.json` x) i filen.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Som standard genereras alla icke-omuppspelnings spårnings händelser. Mängden data kan minskas genom att ställa in `Host.Triggers.DurableTask` till `"Warning"` eller `"Error"` i så fall kommer spårning av händelser bara att genereras för exceptionella situationer.

Om du vill aktivera sändning av utförliga Dirigerings händelser `LogReplayEvents` kan du `true` ställa in på i `host.json` filen under `durableTask` som visas:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Som standard samplas Application Insights telemetri av Azure Functions runtime för att undvika sändning av data för ofta. Detta kan göra att spårnings informationen går förlorad när många livs cykel händelser inträffar under en kort tids period. I [artikeln Azure Functions övervakning](../functions-monitoring.md#configure-sampling) förklaras hur du konfigurerar det här beteendet.

### <a name="single-instance-query"></a>Fråga för enskild instans

I följande fråga visas historiska spårnings data för en enda instans av funktionen Orchestration i [Hello-sekvensen](durable-functions-sequence.md) . Det skrivs med hjälp av [AIQL (Application Insights Query Language)](https://aka.ms/LogAnalyticsLanguageReference). Den filtrerar fram uppspelnings körningen så att bara den *logiska* körnings Sök vägen visas. Händelser kan sorteras genom att sortera `timestamp` efter `sequenceNumber` och som visas i frågan nedan:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Resultatet är en lista över spårnings händelser som visar körnings vägen för dirigeringen, inklusive alla aktivitets funktioner ordnade efter körnings tiden i stigande ordning.

![Application Insights fråga](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Instans sammanfattnings fråga

Följande fråga visar status för alla Dirigerings instanser som kördes inom ett angivet tidsintervall.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Resultatet är en lista över instans-ID: n och deras aktuella körnings status.

![Application Insights fråga](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Loggning

Det är viktigt att du behåller Orchestrator-omuppspelnings beteendet i åtanke när du skriver loggar direkt från en Orchestrator-funktion. Överväg till exempel följande Orchestrator-funktion:

### <a name="precompiled-c"></a>FörkompileradeC#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C#Över

```csharp
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Den resulterande logg informationen kommer att se ut ungefär så här:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Kom ihåg att även om loggarna som ska anropa F1, F2 och F3, så kallas dessa funktioner *bara första* gången de påträffas. Efterföljande anrop som sker under uppspelningen hoppas över och utdata spelas upp i Orchestrator-logiken.

Om du bara vill logga in på en icke-omuppspelnings körning kan du skriva ett villkors uttryck för att `IsReplaying` endast `false`logga om är. Överväg exemplet ovan, men den här gången med repetitions kontroller.

#### <a name="precompiled-c"></a>FörkompileradeC#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Med den här ändringen ser loggen ut så här:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Anpassad status

Med anpassad Orchestration-status kan du ange ett anpassat status värde för din Orchestrator-funktion. Den här statusen tillhandahålls via http-status frågans API eller `DurableOrchestrationClient.GetStatusAsync` API: et. Den anpassade Orchestration-statusen möjliggör bättre övervakning av Orchestrator-funktioner. Till exempel kan Orchestrator-funktions koden innehålla `DurableOrchestrationContext.SetCustomStatus` anrop för att uppdatera förloppet för en tids krävande åtgärd. En klient, till exempel en webb sida eller ett annat externt system, kan sedan regelbundet fråga HTTP-status frågans API: er för mer information om förloppet. Ett exempel som `DurableOrchestrationContext.SetCustomStatus` använder finns nedan:

### <a name="precompiled-c"></a>FörkompileradeC#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Medan dirigeringen körs kan externa klienter hämta den anpassade statusen:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienterna får följande svar:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Den anpassade statusen för nytto lasten är begränsad till 16 KB UTF-16 JSON-text eftersom den måste kunna få plats i en Azure Table Storage-kolumn. Du kan använda extern lagring om du behöver större nytto Last.

## <a name="debugging"></a>Felsökning

Azure Functions stöder fel söknings funktions kod direkt och samma stöd vidarebefordrar till Durable Functions, oavsett om de körs i Azure eller lokalt. Det finns dock några beteenden som kan vara medvetna om vid fel sökning:

* **Spela upp**igen: Orchestrator-funktioner [spelas regelbundet upp](durable-functions-orchestrations.md#reliability) när nya indata tas emot. Det innebär att en enda *logisk* körning av en Orchestrator-funktion kan resultera i att samma Bryt punkt används flera gånger, särskilt om den har ställts in tidigt i funktions koden.
* **Väntar**: När ett `await` inträffar i en Orchestrator-funktion, ger den kontrollen tillbaka till Dispatchern för den varaktiga aktivitets ramverket. Om det här är första gången ett visst `await` har påträffats, återupptas *aldrig* den associerade aktiviteten. Eftersom aktiviteten aldrig återupptas är det inte möjligt att stega *över* await (F10 i Visual Studio). Att stega över fungerar bara när en uppgift spelas upp.
* **Timeout för meddelande meddelande**: Durable Functions används internt i kö för att köra funktioner för Orchestrator, aktivitet och entitet. I en miljö med flera virtuella datorer kan en annan virtuell dator Hämta meddelandet, vilket resulterar i dubbla körningar om du bryter mot fel sökningen under längre tids perioder. Det här beteendet finns även för vanliga funktioner för kö-utlösare, men det är viktigt att du pekar på den här kontexten eftersom köerna är en implementerings detalj.
* **Stoppa och starta**: Meddelanden i varaktiga funktioner kvarstår mellan fel söknings sessioner. Om du slutar felsöka och avslutar den lokala värd processen medan en varaktig funktion körs, kan den funktionen köras igen automatiskt i en framtida felsökningssession. Detta kan vara förvirrande när det inte förväntas. Att rensa alla meddelanden från [interna lagrings köer](durable-functions-perf-and-scale.md#internal-queue-triggers) mellan fel söknings sessioner är en teknik för att undvika det här beteendet.

> [!TIP]
> Om du ställer in Bryt punkter i Orchestrator-funktioner, om du bara vill bryta vid icke-omuppspelningar, kan du ange en villkorlig Bryt punkt som `IsReplaying` bara `false`delar om är.

## <a name="storage"></a>Storage

Durable Functions lagrar som standard tillstånd i Azure Storage. Det innebär att du kan kontrol lera statusen för dina dirigeringar med hjälp av verktyg som [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer skärm bild](./media/durable-functions-diagnostics/storage-explorer.png)

Detta är användbart för fel sökning eftersom du ser exakt vilket tillstånd ett dirigering kan vara i. Meddelanden i köerna kan också undersökas för att ta reda på vilket arbete som väntar (eller fastnat i vissa fall).

> [!WARNING]
> Även om det är praktiskt att se körnings historiken i Table Storage, Undvik att ta något beroende på den här tabellen. Den kan ändras när Durable Functions tillägget utvecklas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om övervakning i Azure Functions](../functions-monitoring.md)
