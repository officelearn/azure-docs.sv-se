---
title: Diagnostik i varaktiga funktioner – Azure
description: Lär dig hur du felsöker problem med tillägget varaktiga funktioner för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 066ae6ffba64c7dd71c982172f7cd15a881809b4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036808"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostik i varaktiga funktioner i Azure

Det finns flera alternativ för att diagnostisera problem med [varaktiga funktioner](durable-functions-overview.md). Vissa av dessa alternativ är desamma för vanliga funktioner och vissa av dem är unika för varaktiga funktioner.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../application-insights/app-insights-overview.md) är det rekommendera sättet att göra diagnostik och övervakning i Azure Functions. Samma gäller för varaktiga funktioner. En översikt över hur du kan använda Application Insights i din funktionsapp, se [övervaka Azure Functions](../functions-monitoring.md).

Azure Functions varaktiga Extension genererar även *spårning av händelser* som gör det möjligt att spåra slutpunkt till slutpunkt-körningen av en orkestrering. Dessa kan hittas och fråga med hjälp av den [Application Insights Analytics](../../azure-monitor/app/analytics.md) verktyg i Azure-portalen.

### <a name="tracking-data"></a>Spårningsdata

Varje livscykel händelse av en orchestration-instans orsakar en spårning händelse som ska skrivas till den **spårningar** samling i Application Insights. Den här händelsen innehåller en **customDimensions** nyttolasten med flera fält.  Fältnamn är alla föregås av ett anpassningsprefix `prop__`.

* **hubName**: Namnet på aktiviteten hubben där din orkestreringar körs.
* **appName**: Namnet på funktionsappen. Detta är användbart när du har flera funktionsappar som delar samma Application Insights-instans.
* **Platsnamn**: Den [distributionsfacket](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) i vilken den aktuella funktionsappen körs. Detta är användbart när du dra nytta av distributionsplatser till version din orkestreringar.
* **functionName**: Namnet på funktionen orchestrator eller aktivitet.
* **egenskaperna functionType**: Typ av funktion, till exempel **Orchestrator** eller **aktivitet**.
* **instanceId**: Unikt ID för orchestration-instans.
* **tillstånd**: Livscykel körningstillstånd instansen. Giltiga värden är:
  * **Schemalagd**: Funktionen har schemalagts för körning men har inte startats ännu.
  * **Igång**: Funktionen har startats men har inte ännu slutförts eller har slutförts.
  * **Slutförts**: Orchestrator har schemalagts för del arbete och väntar tills den avslutats.
  * **Lyssna**: Orchestrator lyssnar på en extern händelsemeddelande.
  * **Slutfört**: Funktionen har slutförts.
  * **Misslyckades**: Funktionen misslyckades med ett fel.
* **Orsak**: Ytterligare data som är associerade med spårningshändelsen. Om en instans är att en extern händelse-meddelande, visar det här fältet namnet på den händelse som väntar. Om en funktion har misslyckats, innehåller den felinformationen.
* **isReplay**: Booleskt värde som anger om det är spårningshändelsen för uppspelat körning.
* **extensionVersion**: Versionen av tillägget varaktiga uppgift. Detta är särskilt viktiga data när reporting möjliga buggar i tillägget. Långvariga instanser kan rapportera flera versioner om en uppdatering inträffar när den körs.
* **sequenceNumber**: Körningen sekvensnumret för en händelse. I kombination med tidsstämpel som kan sortera händelserna efter körningstid. *Observera att det här talet är återställning till noll om värden startar när instansen körs, så det är viktigt att alltid sortera efter tidsstämpel först och sedan sequenceNumber.*

Detaljnivå för att spåra data som skickas till Application Insights kan konfigureras i den `logger` (fungerar 1.x) eller `logging` (fungerar 2.x) delen av den `host.json` filen.

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

Som standard genereras alla icke-repetitionsattacker spårningshändelser. Mängden data som kan minskas genom att ange `Host.Triggers.DurableTask` till `"Warning"` eller `"Error"` då spårning av händelser kommer endast genereras för undantagsfall.

Så här aktiverar du sänder utförlig orchestration repetitionsattacker händelser, den `LogReplayEvents` kan anges till `true` i den `host.json` filen under `durableTask` enligt:

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
> Som standard in Application Insights telemetry av Azure Functions-körning för att undvika att sända data för ofta. Detta kan orsaka spårningsinformation går förlorade när många Livscykelhändelser sker i en kort tidsperiod. Den [Azure Functions Monitoring artikeln](../functions-monitoring.md#configure-sampling) förklarar hur du konfigurerar det här beteendet.

### <a name="single-instance-query"></a>Instans-fråga

Följande fråga visar av historiska spårningsdata för en enda instans av den [Hello sekvens](durable-functions-sequence.md) fungera dirigering. Den har skrivits med hjälp av den [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Filtreras bort repetitionsattacker körning så att endast den *logiska* exekveringsväg visas. Händelser kan beställas genom att sortera efter `timestamp` och `sequenceNumber` som visas i frågan nedan:

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

Resultatet är en lista över spårning av händelser som visar körningssökvägen för orkestrering, inklusive eventuella Aktivitetsfunktioner sorterade efter körningstid i stigande ordning.

![Application Insights-fråga](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Sammanfattning av frågan för instans

Följande fråga visar status för alla orchestration-instanser som körs i ett angivet tidsintervall.

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

Resultatet är en lista över instans-ID: N och deras aktuella Körningsstatus.

![Application Insights-fråga](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Loggning

Det är viktigt att orchestrator repetitionsattacker beteende tänka på när du skriver loggarna direkt från en orchestrator-funktion. Anta exempelvis att följande orchestrator-funktion:

### <a name="c"></a>C#

```cs
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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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

Resulterande loggdata ska se ut ungefär så här:

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
> Kom ihåg att även om loggarna anspråk som ska ska anropa F1, F2 och F3, dessa funktioner är endast *faktiskt* kallas första gången de påträffas. Efterföljande anrop som sker under repetitionsattacker hoppas över och utdata spelas upp orchestrator-logik.

Om du vill att endast logga in icke-repetitionsattacker körning, du kan skriva villkorsuttryck till loggen endast om `IsReplaying` är `false`. Överväg att i exemplet ovan, men den här gången med repetitionsattacker kontroller.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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

Med den här ändringen är till loggutdata följande:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Anpassade Status

Anpassad orkestreringsstatus kan du ange ett värde för anpassad status för orchestrator-funktion. Den här statusen tillhandahålls via HTTP-status frågan API eller `DurableOrchestrationClient.GetStatusAsync` API. Anpassad orkestreringsstatus aktiverar mer omfattande övervakning för orchestrator-funktioner. Till exempel Funktionskoden orchestrator kan innehålla `DurableOrchestrationContext.SetCustomStatus` anrop till uppdateringens förlopp för en långvarig åtgärd. En klient som en webbsida eller andra externa system kunde sedan regelbundet fråga HTTP-status frågan API: er för bättre statusinformation. Ett exempel som använder `DurableOrchestrationContext.SetCustomStatus` finns nedan:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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

När orchestration körs, kan externa klienter hämta den här anpassade status:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienter kommer att få följande svar:

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
> Anpassade nyttolasten är begränsad till 16 KB av UTF-16 JSON-texten eftersom den måste kunna få plats i en kolumn för Azure Table Storage. Du kan använda extern lagring om du behöver större nyttolast.

## <a name="debugging"></a>Felsökning

Azure Functions stöder felsökning Funktionskoden direkt och som har stöd för samma följer framåt till varaktiga funktioner, oavsett om som körs i Azure eller lokalt. Det finns dock några beteenden känna till när du felsöker:

* **Återuppspelning**: Orchestrator-funktioner regelbundet spela upp när nya indata tas emot. Det innebär att en enda *logiska* körningen av en orchestrator-funktion kan resultera i att träffa den samma brytpunkten flera gånger, särskilt om det angetts tidigt i funktionskoden.
* **Await**: När en `await` är uppstår det ger kontroll tillbaka till varaktiga uppgift Framework-avsändaren. Om det här är första gången en viss `await` har uppstått, associerade aktiviteten är *aldrig* återupptas. Eftersom uppgiften återupptas aldrig, Stega dig *över* await (F10 i Visual Studio) är inte faktiskt möjligt. Stega dig över fungerar bara när en uppgift som spelas.
* **Meddelanden tidsgränser**: Varaktiga funktioner använder internt Kömeddelanden till enheten körningen av både orchestrator-funktioner och Aktivitetsfunktioner. I en miljö med flera virtuella datorer, kan det orsaka att hämta meddelandet, vilket resulterar i dubbla körning på en annan virtuell dator att dela i Felsökning för längre tid. Det här beteendet finns för regelbundna köutlösare-funktioner, men det är viktigt att påpeka i den här kontexten eftersom köer är en implementeringsdetalj.

> [!TIP]
> När du anger brytpunkter, om du vill avbryta endast för icke-repetitionsattacker körning, du kan konfigurera en villkorlig brytpunkt att endast om radbrytningar `IsReplaying` är `false`.

## <a name="storage"></a>Storage

Som standard lagrar varaktiga funktioner tillstånd i Azure Storage. Det innebär att du kan granska status för din orkestreringar med verktyg som [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer skärmbild](./media/durable-functions-diagnostics/storage-explorer.png)

Detta är användbart för felsökning eftersom du se exakt vilka tillstånd som en orkestrering kan finnas i. Meddelanden i köer kan också undersökas om du vill veta vilka arbete är i vänteläge (eller fastnar i vissa fall).

> [!WARNING]
> Det är praktiskt att se körningshistorik i table storage, Undvik att ta alla beroenden på den här tabellen. Det kan ändras när tillägget varaktiga funktioner utvecklas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder varaktiga timers](durable-functions-timers.md)
