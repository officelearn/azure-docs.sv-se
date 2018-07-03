---
title: Diagnostik i varaktiga funktioner – Azure
description: Lär dig hur du felsöker problem med tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: ce5acda7e2beca1f3d6367708d5b96a5275b2c7f
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340709"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostik i varaktiga funktioner (Azure Functions)

Det finns flera alternativ för att diagnostisera problem med [varaktiga funktioner](durable-functions-overview.md). Vissa av dessa alternativ är desamma för vanliga funktioner och vissa av dem är unika för varaktiga funktioner.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) är det rekommendera sättet att göra diagnostik och övervakning i Azure Functions. Samma gäller för varaktiga funktioner. En översikt över hur du kan använda Application Insights i din funktionsapp, se [övervaka Azure Functions](functions-monitoring.md).

Azure Functions varaktiga Extension genererar även *spårning av händelser* som gör det möjligt att spåra slutpunkt till slutpunkt-körningen av en orkestrering. Dessa kan hittas och fråga med hjälp av den [Application Insights Analytics](../application-insights/app-insights-analytics.md) verktyg i Azure-portalen.

### <a name="tracking-data"></a>Spårningsdata

Varje livscykel händelse av en orchestration-instans orsakar en spårning händelse som ska skrivas till den **spårningar** samling i Application Insights. Den här händelsen innehåller en **customDimensions** nyttolasten med flera fält.  Fältnamn är alla föregås av ett anpassningsprefix `prop__`.

* **hubName**: namnet på aktiviteten hubben där din orkestreringar körs.
* **appName**: namnet på funktionsappen. Detta är användbart när du har flera funktionsappar som delar samma Application Insights-instans.
* **Platsnamn**: den [distributionsfacket](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) i vilken den aktuella funktionsappen körs. Detta är användbart när du dra nytta av distributionsplatser till version din orkestreringar.
* **functionName**: namnet på funktionen orchestrator eller aktivitet.
* **egenskaperna functionType**: typ av funktion, till exempel **Orchestrator** eller **aktivitet**.
* **instanceId**: unikt ID för orchestration-instans.
* **tillstånd**: livscykel körningstillstånd instansen. Giltiga värden är:
    * **Schemalagda**: funktionen har schemalagts för körning men har inte startats ännu.
    * **Igång**: funktionen har startats men har inte ännu har slutförts eller har slutförts.
    * **Slutförts**: orchestrator har schemalagts del arbete och väntar tills den avslutats.
    * **Lyssna**: orchestrator lyssnar efter en extern händelse-meddelande.
    * **Slutfört**: funktionen har slutförts.
    * **Det gick inte**: funktionen misslyckades med ett fel.
* **Orsak**: ytterligare data som är associerade med spårningshändelsen. Om en instans är att en extern händelse-meddelande, visar det här fältet namnet på den händelse som väntar. Om en funktion har misslyckats, innehåller den felinformationen.
* **isReplay**: booleskt värde som anger om det är spårningshändelsen för återupprepas körning.
* **extensionVersion**: version av tillägget varaktiga uppgift. Detta är särskilt viktiga data när reporting möjliga buggar i tillägget. Långvariga instanser kan rapportera flera versioner om en uppdatering inträffar när den körs. 
* **sequenceNumber**: körning sekvensnumret för en händelse. I kombination med tidsstämpel som kan sortera händelserna efter körningstid. *Observera att det här talet är återställning till noll om värden startar när instansen körs, så det är viktigt att alltid sortera efter tidsstämpel först och sedan sequenceNumber.*

Detaljnivå för att spåra data som skickas till Application Insights kan konfigureras i den `logger` delen av den `host.json` filen.

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

Som standard genereras alla icke-repetitionsattacker spårningshändelser. Mängden data som kan minskas genom att ange `Host.Triggers.DurableTask` till `"Warning"` eller `"Error"` då spårning av händelser kommer endast genereras för undantagsfall.

Så här aktiverar du sänder utförlig orchestration repetitionsattacker händelser, den `LogReplayEvents` kan anges till `true` i den `host.json` filen under `durableTask` enligt:

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

> [!NOTE]
> Som standard in Application Insights telemetry av Azure Functions-körning för att undvika att sända data för ofta. Detta kan orsaka spårningsinformation går förlorade när många Livscykelhändelser sker i en kort tidsperiod. Den [Azure Functions Monitoring artikeln](functions-monitoring.md#configure-sampling) förklarar hur du konfigurerar det här beteendet.

### <a name="single-instance-query"></a>Instans-fråga

Följande fråga visar av historiska spårningsdata för en enda instans av den [Hello sekvens](durable-functions-sequence.md) fungera dirigering. Den har skrivits med hjälp av den [Application Insights Query Language (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Filtreras bort repetitionsattacker körning så att endast den *logiska* exekveringsväg visas. Händelser kan beställas genom att sortera efter `timestamp` och `sequenceNumber` som visas i frågan nedan: 

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

![Application Insights-fråga](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


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

![Application Insights-fråga](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Loggning

Det är viktigt att orchestrator repetitionsattacker beteende tänka på när du skriver loggarna direkt från en orchestrator-funktion. Anta exempelvis att följande orchestrator-funktion:

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivityAsync("F1");
    context.log("Calling F2.");
    yield context.df.callActivityAsync("F2");
    context.log("Calling F3.");
    yield context.df.callActivityAsync("F3");
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

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Med den här ändringen är till loggutdata följande:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Den `IsReplaying` egenskapen är ännu inte tillgänglig i JavaScript.

## <a name="custom-status"></a>Anpassade Status

Anpassad orkestreringsstatus kan du ange ett värde för anpassad status för orchestrator-funktion. Den här statusen tillhandahålls via HTTP-status frågan API eller `DurableOrchestrationClient.GetStatusAsync` API. Anpassad orkestreringsstatus aktiverar mer omfattande övervakning för orchestrator-funktioner. Till exempel Funktionskoden orchestrator kan innehålla `DurableOrchestrationContext.SetCustomStatus` anrop till uppdateringens förlopp för en långvarig åtgärd. En klient som en webbsida eller andra externa system kunde sedan regelbundet fråga HTTP-status frågan API: er för bättre statusinformation. Ett exempel som använder `DurableOrchestrationContext.SetCustomStatus` finns nedan:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
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
>  Anpassade nyttolasten är begränsad till 16 KB av UTF-16 JSON-texten eftersom den måste kunna få plats i en kolumn för Azure Table Storage. Du kan använda extern lagring om du behöver större nyttolast.

## <a name="debugging"></a>Felsökning

Azure Functions stöder felsökning Funktionskoden direkt och som har stöd för samma följer framåt till varaktiga funktioner, oavsett om som körs i Azure eller lokalt. Det finns dock några beteenden känna till när du felsöker:

* **Spela upp**: Orchestrator-funktioner spela upp regelbundet när nya indata tas emot. Det innebär att en enda *logiska* körningen av en orchestrator-funktion kan resultera i att träffa den samma brytpunkten flera gånger, särskilt om det angetts tidigt i funktionskoden.
* **Await**: varje gång en `await` är uppstår det ger kontroll tillbaka till varaktiga uppgift Framework-avsändaren. Om det här är första gången en viss `await` har uppstått, associerade aktiviteten är *aldrig* återupptas. Eftersom uppgiften återupptas aldrig, Stega dig *över* await (F10 i Visual Studio) är inte faktiskt möjligt. Stega dig över fungerar bara när en uppgift som spelas.
* **Meddelanden tidsgränser**: varaktiga funktioner använder internt Kömeddelanden till enheten körningen av både orchestrator-funktioner och Aktivitetsfunktioner. I en miljö med flera virtuella datorer, kan det orsaka att hämta meddelandet, vilket resulterar i dubbla körning på en annan virtuell dator att dela i Felsökning för längre tid. Det här beteendet finns för regelbundna köutlösare-funktioner, men det är viktigt att påpeka i den här kontexten eftersom köer är en implementeringsdetalj.

> [!TIP]
> När du anger brytpunkter, om du vill avbryta endast för icke-repetitionsattacker körning, du kan konfigurera en villkorlig brytpunkt att endast om radbrytningar `IsReplaying` är `false`.

## <a name="storage"></a>Storage

Som standard lagrar varaktiga funktioner tillstånd i Azure Storage. Det innebär att du kan granska status för din orkestreringar med verktyg som [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer skärmbild](media/durable-functions-diagnostics/storage-explorer.png)

Detta är användbart för felsökning eftersom du se exakt vilka tillstånd som en orkestrering kan finnas i. Meddelanden i köer kan också undersökas om du vill veta vilka arbete är i vänteläge (eller fastnar i vissa fall).

> [!WARNING]
> Det är praktiskt att se körningshistorik i table storage, Undvik att ta alla beroenden på den här tabellen. Det kan ändras när tillägget varaktiga funktioner utvecklas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder varaktiga timers](durable-functions-timers.md)