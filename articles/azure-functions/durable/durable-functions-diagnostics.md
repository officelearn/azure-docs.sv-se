---
title: Diagnostik i varaktiga funktioner - Azure
description: Lär dig hur du diagnostiserar problem med tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278199"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostik i Durable Functions i Azure

Det finns flera alternativ för att diagnostisera problem med [varaktiga funktioner](durable-functions-overview.md). Vissa av dessa alternativ är desamma för vanliga funktioner medan andra är unika för Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är det rekommenderade sättet att göra diagnostik och övervakning i Azure Functions. Detsamma gäller för varaktiga funktioner. En översikt över hur du använder Application Insights i funktionsappen finns i [Övervaka Azure-funktioner](../functions-monitoring.md).

Azure Functions Durable Extension avger också *spårningshändelser* som gör att du kan spåra körningen från slutna till slutna funktioner av en orkestrering. Dessa spårningshändelser kan hittas och efterfrågas med hjälp av [application insights Analytics-verktyget](../../azure-monitor/app/analytics.md) i Azure-portalen.

### <a name="tracking-data"></a>Spåra data

Varje livscykelhändelse för en orchestration-instans gör att en **spårningshändelse** skrivs till spårningssamlingen i Application Insights. Den här händelsen innehåller en **anpassad Nyttolast** med flera fält.  Fältnamn är alla förberedda `prop__`med .

* **hubName**: Namnet på aktivitetshubben där orkestreringen körs.
* **appName**: Namnet på funktionsappen. Det här fältet är användbart när du har flera funktionsappar som delar samma Application Insights-instans.
* **slotName**: [Distributionsplatsen](../functions-deployment-slots.md) där den aktuella funktionsappen körs. Det här fältet är användbart när du utnyttjar distributionsplatser för att version av dina orkestreringar.
* **funktionNamn**: Namnet på orchestrator eller aktivitetsfunktionen.
* **functionType**: Funktionens typ, till exempel **Orchestrator** eller **Activity**.
* **instanceId**: Det unika ID:et för orchestration-instansen.
* **tillstånd**: Livscykelkörningstillståndet för instansen. Giltiga värden inkluderar:
  * **Schemalagd**: Funktionen har schemalagts för körning men har inte börjat köras ännu.
  * **Startad**: Funktionen har börjat köras men har ännu inte väntat eller slutförts.
  * **Efterlängtad**: Orchestrator har planerat en del arbete och väntar på att slutföra.
  * **Lyssna:** Orchestrator lyssnar efter en extern händelse anmälan.
  * **Slutförd**: Funktionen har slutförts.
  * **Misslyckades**: Funktionen misslyckades med ett fel.
* **orsak**: Ytterligare data som är associerade med spårningshändelsen. Om en instans till exempel väntar på ett meddelande om externa händelser anger det här fältet namnet på den händelse som den väntar på. Om en funktion har misslyckats innehåller det här fältet felinformationen.
* **isReplay**: Boolean-värde som anger om spårningshändelsen är för omspelad körning.
* **extensionVersion**: Versionen av tillägget Hållbar uppgift. Versionsinformationen är särskilt viktig data när du rapporterar möjliga fel i tillägget. Tidskrävande instanser kan rapportera flera versioner om en uppdatering inträffar medan den körs.
* **sequenceNumber**: Körningssekvensnummer för en händelse. Kombinerat med tidsstämpeln hjälper till att beställa händelserna efter körningstid. *Observera att det här numret återställs till noll om värden startar om medan instansen körs, så det är viktigt att alltid sortera efter tidsstämpel först och sedan sequenceNumber.*

Verbositeten för att spåra data som skickas till Application `logger` Insights kan konfigureras `logging` i avsnittet (Funktioner 1.x) eller (Funktioner 2.0) i `host.json` filen.

#### <a name="functions-10"></a>Funktioner 1,0

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

#### <a name="functions-20"></a>Funktioner 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Som standard avges alla spårningshändelser som inte inte är repriser. Datavolymen kan minskas genom `Host.Triggers.DurableTask` att `"Warning"` `"Error"` ställa in på eller i vilket fall spårningshändelser endast kommer att släppas ut för exceptionella situationer.

Om du vill aktivera avgivande händelser `LogReplayEvents` för utförlig `true` orkestreringsuppspelning kan den ställas in i `host.json` filen under `durableTask` enligt bilden:

#### <a name="functions-10"></a>Funktioner 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funktioner 2.0

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
> Som standard samhÃ¤ndes application insights-telemetri av Azure Functions-körningen för att undvika att data avges för ofta. Detta kan leda till att spårningsinformation går förlorad när många livscykelhändelser inträffar på kort tid. I [artikeln Övervakning av Azure Functions Monitoring](../functions-monitoring.md#configure-sampling) beskrivs hur du konfigurerar det här beteendet.

### <a name="single-instance-query"></a>Fråga om en instans

Följande fråga visar historiska spårningsdata för en enda instans av funktionen [Hello Sequence](durable-functions-sequence.md) orchestration. Det är skrivet med hjälp av [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Det filtrerar bort repriskörning så att endast den *logiska* körningssökvägen visas. Händelser kan ordnas efter `timestamp` `sequenceNumber` sortering efter och som visas i frågan nedan:

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

Resultatet är en lista över spårningshändelser som visar körningssökvägen för orkestreringen, inklusive alla aktivitetsfunktioner som beställts av körningstiden i stigande ordning.

![Fråga om programinsikter](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Sammanfattningsfråga för instans

Följande fråga visar statusen för alla orchestration-instanser som kördes i ett angivet tidsintervall.

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

Resultatet är en lista över instans-ID:er och deras aktuella körningsstatus.

![Fråga om programinsikter](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Loggning

Det är viktigt att hålla orchestrator replay beteende i åtanke när du skriver loggar direkt från en orchestrator funktion. Tänk till exempel på följande orchestrator-funktion:

### <a name="precompiled-c"></a>Förkompilerad C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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

### <a name="c-script"></a>C# Skript

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
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

### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

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

De resulterande loggdata kommer att se ut ungefär så här som följande exempelutdata:

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
> Kom ihåg att medan loggarna hävdar att ringa F1, F2 och F3, dessa funktioner *är bara faktiskt* kallas första gången de påträffas. Efterföljande anrop som sker under repris hoppas över och utdata spelas upp till orchestrator-logiken.

Om du bara vill logga in på körning som inte är `IsReplaying` repriserar kan du bara skriva ett villkorsuttryck för att logga om det är `false`. Tänk på exemplet ovan, men den här gången med repriskontroller.

#### <a name="precompiled-c"></a>Förkompilerad C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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
    IDurableOrchestrationContext context,
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

#### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

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

Med början i Varaktiga funktioner 2.0 har .NET `ILogger` orchestrator-funktioner också möjlighet att skapa en som automatiskt filtrerar bort loggsatser under repris. Den här automatiska filtrningen görs med API:et. `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)`

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Med de tidigare nämnda ändringarna är loggutdata följande:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> De tidigare C#-exemplen är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

## <a name="custom-status"></a>Anpassad status

Med anpassad orchestration-status kan du ange ett anpassat statusvärde för din orchestrator-funktion. Den här statusen tillhandahålls via HTTP-statusfrågans API eller API:et. `IDurableOrchestrationClient.GetStatusAsync` Den anpassade orchestration status möjliggör rikare övervakning för orchestrator funktioner. Orchestrator-funktionskoden kan till `IDurableOrchestrationContext.SetCustomStatus` exempel innehålla anrop för att uppdatera förloppet för en tidskrävande åtgärd. En klient, till exempel en webbsida eller ett annat externt system, kan sedan regelbundet fråga HTTP-statusfA:erna för mer information om förlopp. Nedan finns `IDurableOrchestrationContext.SetCustomStatus` ett prov som använder:

### <a name="precompiled-c"></a>Förkompilerad C #

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Det föregående C#-exemplet är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

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

Medan orkestrering körs kan externa klienter hämta den här anpassade statusen:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienter får följande svar:

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
> Den anpassade statusnyttolasten är begränsad till 16 KB AV UTF-16 JSON-text eftersom den måste kunna få plats i en Azure Table Storage-kolumn. Du kan använda extern lagring om du behöver större nyttolast.

## <a name="debugging"></a>Felsökning

Azure Functions stöder felsökning av funktionskod direkt och samma support överförs till varaktiga funktioner, oavsett om den körs i Azure eller lokalt. Det finns dock några beteenden att vara medveten om när felsökning:

* **Spela upp:** Orchestrator-funktioner spelas regelbundet [upp](durable-functions-orchestrations.md#reliability) när nya indata tas emot. Detta innebär att en enda *logisk* körning av en orchestrator-funktion kan resultera i att träffa samma brytpunkt flera gånger, särskilt om den är inställd tidigt i funktionskoden.
* **Väntar:** När `await` en påträffas i en orchestrator-funktion ger den kontroll tillbaka till dispatcher för hållbar aktivitetsramverk. Om det är första `await` gången en viss aktivitet har påträffats återupptas *aldrig* den associerade aktiviteten. Eftersom uppgiften aldrig återupptas är det inte möjligt att kliva *över* invänningen (F10 i Visual Studio). Att kliva över fungerar bara när en uppgift spelas upp igen.
* **Tidsutskrifter**för meddelanden : Varaktiga funktioner använder internt kömeddelanden för att driva körning av orchestrator-, aktivitets- och entitetsfunktioner. I en miljö med flera virtuella datorer kan felsökning i felsökning under längre tidsperioder leda till att en annan virtuell dator hämtar meddelandet, vilket resulterar i dubblettkörning. Det här beteendet finns även för vanliga köutlösarefunktioner, men det är viktigt att påpeka i det här sammanhanget eftersom köerna är en implementeringsdetalj.
* **Stoppa och starta**: Meddelanden i varaktiga funktioner kvarstår mellan felsökningssessioner. Om du slutar felsöka och avsluta den lokala värdprocessen medan en varaktig funktion körs, kan den funktionen köras automatiskt i en framtida felsökningssession. Detta kan vara förvirrande när det inte förväntas. Att rensa alla meddelanden från de [interna lagringsköerna mellan felsökningssessioner](durable-functions-perf-and-scale.md#internal-queue-triggers) är en teknik för att undvika detta.

> [!TIP]
> När du anger brytpunkter i orchestrator-funktioner kan du ange en villkorlig brytpunkt som bara bryts `IsReplaying` `false`om den är .

## <a name="storage"></a>Lagring

Som standard lagrar varaktiga funktioner tillstånd i Azure Storage. Det här problemet innebär att du kan inspektera tillståndet för dina orkestreringar med hjälp av verktyg som [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Skärmbild av Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Detta är användbart för felsökning eftersom du ser exakt vilket tillstånd en orkestrering kan vara i. Meddelanden i köerna kan också undersökas för att ta reda på vilket arbete som väntar (eller fastnat i vissa fall).

> [!WARNING]
> Det är praktiskt att se körningshistorik i tabelllagring, men undvik att ta något beroende av den här tabellen. Det kan ändras i takt med att tillägget Varaktiga funktioner utvecklas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om övervakning i Azure Functions](../functions-monitoring.md)
