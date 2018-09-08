---
title: Översikt över varaktiga funktioner – Azure
description: Introduktion till tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 136316feab5a08308a9f10e499f645aaee0c90d3
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093251"
---
# <a name="durable-functions-overview"></a>Översikt över varaktiga funktioner

*Varaktiga funktioner* är en utökning av [Azure Functions](functions-overview.md) och [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) som hjälper dig att skriva tillståndskänsliga funktioner i en serverfri miljö. Tillägget hanterar tillstånd, kontrollpunkter och startas om för dig.

Tillägget kan du definiera tillståndskänsliga arbetsflöden i en ny typ av funktion som kallas en [ *orchestrator-funktion*](durable-functions-types-features-overview.md#orchestrator-functions). Här följer några av fördelarna med orchestrator-funktioner:

* De definiera arbetsflöden i kod. Inga JSON-scheman eller designers behövs.
* De kan anropa andra funktioner synkront och asynkront. Du kan spara utdata från anropade funktionerna till lokala variabler.
* De automatiskt kontrollpunkt framstegen varje gång som funktionen väntar på dig. Lokal state förloras aldrig om processen återvinns eller den virtuella datorn startas om.

> [!NOTE]
> Varaktiga funktioner är en avancerad tillägg för Azure Functions som inte är lämplig för alla program. Resten av den här artikeln förutsätter att du har en stark bekant med [Azure Functions](functions-overview.md) begrepp och utmaningarna som ingår i program utan Server-utveckling.

I första hand för varaktiga funktioner är förenkla komplexa, tillståndskänsliga samordning problem i program utan server. I följande avsnitt beskrivs några vanliga mönster som kan dra nytta av varaktiga funktioner.

## <a name="pattern-1-function-chaining"></a>Mönster #1: Funktionen länkning

*Funktionen länkning* refererar till mönstret för att köra en serie funktioner i en viss ordning. Utdata för en funktion måste ofta tillämpas till indata för en annan funktion.

![Funktionen chaining diagram](media/durable-functions-overview/function-chaining.png)

Varaktiga funktioner kan du implementera det här mönstret koncist i kod.

#### <a name="c-script"></a>C#-skript

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```
> [!NOTE]
> Det finns vissa skillnader vid skrivning till en förkompilerade varaktiga funktion i C# eller C# exempelskriptet visas innan. En C#-förkompilerad version-funktion kräver varaktiga parametrar som ska vara dekorerad med respektive attribut. Ett exempel är `[OrchestrationTrigger]` attributet för `DurableOrchestrationContext` parametern. Om parametrarna inte är korrekt dekorerad, körningen inte skulle kunna mata in variabler för funktionen och ger fel. Besök [exempel](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) fler exempel.

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const x = yield ctx.df.callActivityAsync("F1");
    const y = yield ctx.df.callActivityAsync("F2", x);
    const z = yield ctx.df.callActivityAsync("F3", y);
    return yield ctx.df.callActivityAsync("F4", z);
});
```

Värdena ”F1”, ”F2”, ”F3” och ”F4” är namnen på andra funktioner i funktionsappen. Kontrollflöde implementeras med hjälp av kodning konstruktioner som normalt krävs. Det vill säga kod körs uppifrån och ned och kan omfatta befintliga språk control flow semantik, som villkor och loopar.  Felhanteringslogik kan ingå i försök/catch/slutligen block.

Den `ctx` parametern ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) tillhandahåller metoder för att anropa andra funktioner efter namn, skicka parametrar och returnera funktionsutdata. Varje gång koden anropar `await`, varaktiga funktioner framework *kontrollpunkter* förloppet för den aktuella funktion-instansen. Om processen eller VM återvinns halvvägs körningen, funktionen instans återupptas från den tidigare `await` anropa. Mer information om detta omstartsbeteende senare.

## <a name="pattern-2-fan-outfan-in"></a>Mönster #2: Fan-in/fan-i

*FAN-in/fan-i* refererar till mönstret för att köra flera funktioner parallellt och sedan att vänta tills alla ska slutföras.  Vissa aggregering arbete utförs ofta i resultatet som returneras från funktioner.

![FAN-in/fan-i diagram](media/durable-functions-overview/fan-out-fan-in.png)

Med normal funktion, fläkt kan du göra genom att använda funktionen Skicka flera meddelanden till en kö. Fläkt i är dock mycket mer utmanande. Du skulle behöva skriva kod för att spåra när de kö-utlösta funktionerna avslutas och lagra funktionen utdata. Tillägget varaktiga funktioner hanterar det här mönstret med relativt enkel kod.

#### <a name="c-script"></a>C#-skript

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield ctx.df.callActivityAsync("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(ctx.df.callActivityAsync("F2", workBatch[i]));
    }

    yield ctx.df.task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield ctx.df.callActivityAsync("F3", sum);
});
```

Fan-out arbetet ska distribueras till flera instanser av funktionen `F2`, och vad som ska spåras med hjälp av en dynamisk lista över aktiviteter. .NET `Task.WhenAll` API anropas för att vänta tills alla de anropade funktionerna ska slutföras. Sedan `F2`funktionen matar ut aggregeras i dynamiska uppgiftslistan och skickas till den `F3` funktion.

Automatiska kontrollpunkter som sker på den `await` anropa `Task.WhenAll` säkerställer att alla krasch eller starta om datorn halvvägs inte kräver en omstart av någon redan slutfört uppgifterna.

## <a name="pattern-3-async-http-apis"></a>Mönster #3: Async HTTP API: er

Mönstret tredje handlar om problemet med att samordna tillståndet för långvariga åtgärder med externa klienter. Ett vanligt sätt att implementera det här mönstret är att låta den tidskrävande åtgärden som utlöses av ett HTTP-anrop och omdirigering av klienten till en slutpunkt för status som de kan avsöker om du vill veta när åtgärden har slutförts.

![HTTP API-diagram](media/durable-functions-overview/async-http-api.png)

Varaktiga funktioner innehåller inbyggda API: er som förenklar koden du skriver för att interagera med tidskrävande funktionskörningar. Den [exempel](durable-functions-install.md) visar ett enkelt REST-kommando som kan användas för att starta nya instanser av orchestrator-funktion. När en instans startas exponerar tillägget webhook HTTP APIs frågan status för orchestrator-funktion. I följande exempel visas de REST-kommandona för att starta en initierare och fråga efter dess status. För tydlighetens skull utelämnas vissa detaljer i exemplet.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Eftersom tillståndet är hanterad av körningen varaktiga funktioner behöver du implementera dina egna status spåra mekanism.

Även om tillägget varaktiga funktioner har inbyggda webhooks för att hantera tidskrävande orkestreringar, du kan implementera det här mönstret själv med din egen function-utlösare (t.ex http-, kö eller Event Hub) och `orchestrationClient` bindning. Du kan exempelvis använda ett kömeddelande för att utlösa avslutning.  Eller så kan du använda en HTTP-utlösare som skyddas av en princip för Azure Active Directory-autentisering i stället för den inbyggda webhooks som använder en genererad nyckel för autentisering. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametern är ett värde från den `orchestrationClient` utdata bindning, som ingår i tillägget varaktiga funktioner. Det ger metoder för Start, för att skicka händelser till, avbryts och fråga efter nya eller befintliga orchestrator-funktion-instanser. I exemplet ovan tar en HTTP-funktion som utlöses en `functionName` värde från inkommande URL och pass värdet [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Den här bindningen API returnerar sedan ett svar som innehåller en `Location` rubrik och ytterligare information om den instans som kan användas senare att leta upp status för igång instansen eller avsluta den.

## <a name="pattern-4-monitoring"></a>Mönstret #4: övervaka

Mönstret övervakaren refererar till en flexibel *återkommande* processen i ett arbetsflöde – till exempel avsökning tills vissa villkor uppfylls. En vanlig timerutlösare kan lösa ett enkelt scenario, till exempel en regelbunden rensningsjobb men intervallet är statisk och hantera instans livslängd blir komplex. Varaktiga funktioner möjliggör flexibel återkommande intervall, uppgiftshantering livslängd och möjligheten att skapa flera Övervakare processer från en enda orchestration.

Ett exempel skulle återställa det tidigare async HTTP API-scenariot. I stället för att exponera en slutpunkt för en extern klient kan övervaka en tidskrävande åtgärd kan förbrukar tidskrävande övervakaren en extern slutpunkt väntar på att vissa tillståndsändring.

![Övervaka diagram](media/durable-functions-overview/monitor.png)

Använder varaktiga funktioner, kan flera bildskärmar som använder godtyckliga slutpunkter skapas i några få kodrader. Övervakare kan avsluta körningen när vissa villkor uppfylls, eller avslutas med den [DurableOrchestrationClient](durable-functions-instance-management.md), och deras väntetidsintervallet kan ändras beroende på vissa villkor (d.v.s. exponentiell backoff.) Följande kod implementerar en grundläggande Övervakare.

#### <a name="c-script"></a>C#-skript

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```js
const df = require("durable-functions");
const df = require("moment");

module.exports = df(function*(ctx) {
    const jobId = ctx.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(ctx.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield ctx.df.callActivityAsync("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield ctx.df.callActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(ctx.df.currentUtcDateTime).add(pollingInterval, 's');
        yield ctx.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

När en begäran tas emot, skapas en ny orchestration-instans för den jobb-ID. Instansen avsöker status tills ett villkor uppfylls och slingan har avslutats. En hållbar timer används för att styra avsökningsintervallet. Ytterligare arbetet kan sedan utföras eller dirigering kan sluta. När den `ctx.CurrentUtcDateTime` överskrider den `expiryTime`, det Övervakare upphört att gälla.

## <a name="pattern-5-human-interaction"></a>Mönster #5: Mänsklig interaktion

Många processer involverar någon form av mänsklig interaktion. Knepigt sakerna som rör människor i en automatiserad process är att personer som inte är alltid lika mycket tillgänglig och svarar med molntjänster. Automatiserade processer måste tillåta för detta och de göra ofta det med hjälp av timeout och kompensation logik.

Ett exempel på en affärsprocess som inbegriper mänsklig interaktion är en godkännandeprocess. Godkännande från en chef kan till exempel krävas för en utgiftsrapport som överskrider ett visst belopp. Om du inte godkänner manager inom 72 timmar (kanske de gick på semester), startar en process för eskalering att få godkännande från någon annan (kanske chefens chef).

![Mänsklig interaktionsdiagram](media/durable-functions-overview/approval.png)

Det här mönstret kan implementeras med hjälp av en orchestrator-funktion. Orchestrator använder en [varaktiga timer](durable-functions-timers.md) att begära godkännande och eskalera vid timeout. Det skulle vänta tills en [extern händelse](durable-functions-external-events.md), vilket är det meddelande som genereras av vissa mänsklig interaktion.

#### <a name="c-script"></a>C#-skript

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```js
const df = require("durable-functions");
const df = require('moment');

module.exports = df(function*(ctx) {
    yield ctx.df.callActivityAsync("RequestApproval");

    const dueTime = moment.utc(ctx.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = ctx.df.createTimer(dueTime.toDate());

    const approvalEvent = ctx.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield ctx.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield ctx.df.callActivityAsync("ProcessApproval", approvalEvent.result);
    } else {
        yield ctx.df.callActivityAsync("Escalate");
    }
});
```

Hållbar timern har skapats genom att anropa `ctx.CreateTimer`. Meddelandet tas emot av `ctx.WaitForExternalEvent`. Och `Task.WhenAny` anropas för att bestämma om du vill att eskalera (tidsgräns sker först) eller bearbeta godkännande (godkännande tas emot innan timeout).

En extern klient kan leverera händelseaviseringen till en orchestrator-funktion för att vänta som använder antingen den [inbyggda HTTP APIs](durable-functions-http-api.md#raise-event) eller genom att använda [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) -API en annan funktion:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

## <a name="the-technology"></a>Tekniken

I bakgrunden tillägget varaktiga funktioner är byggt ovanpå den [varaktiga uppgift Framework](https://github.com/Azure/durabletask), ett bibliotek med öppen källkod på GitHub för att skapa varaktiga uppgift orkestreringar. Hur Azure Functions är en serverlös utveckling av Azure WebJobs, är varaktiga funktioner liksom serverlös utvecklingen av varaktiga uppgift Framework. Varaktiga uppgift ramverket används mycket inom Microsoft och utanför samt att automatisera verksamhetskritiska processer. Det är därför lämplig för serverlösa Azure Functions-miljön.

### <a name="event-sourcing-checkpointing-and-replay"></a>Händelsekällor kontrollpunkter och återuppspelning

Orchestrator-funktioner på ett tillförlitligt sätt underhålla sina körningstillstånd med en designmönster för molnet som kallas [händelsekällor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). I stället för att direkt spara den *aktuella* tillståndet för en orkestrering och varaktiga tillägget använder en lagringsplats för endast tillägg för att registrera den *fullständig serie åtgärder* vidtas av funktionen dirigering. Detta har många fördelar, inklusive förbättra prestanda, skalbarhet och tillgänglighet jämfört med ”dumpning” fullständig runtime-tillståndet. Andra fördelar är att tillhandahålla konsekvens för transaktionsdata och bibehåller fullständig granskningshistorik och historik. Granskningshistoriken själva Aktivera tillförlitlig kompenserande åtgärder.

Användning av händelsekällor av det här tillägget är transparent. Under försättsbladen, den `await` operator i en orchestrator-funktion ger kontroll över orchestrator-tråd tillbaka till varaktiga uppgift Framework-avsändaren. Avsändaren sedan genomför alla nya åtgärder som orchestrator-funktion som schemalagts (till exempel anropa en eller flera underordnade funktioner eller schemalägga en hållbar timer) till lagring. Den här transparent commit-åtgärden läggs till i *körningshistorik* för orchestration-instans. Historiken lagras i en lagringstabell. Commit-åtgärden sedan lägger till meddelanden i kö för att schemalägga det faktiska arbetet. Orchestrator-funktion kan nu tas bort från minnet. Faktureringen för den stoppas om du använder Azure Functions Consumption-Plan.  Om det finns mer arbete att göra, funktionen har startats om och dess tillstånd rekonstruerad.

När en orchestration-funktion får mer arbete att göra (till exempel ett svarsmeddelande tas emot eller en hållbar timern upphör) orchestrator aktiveras igen och kör hela funktionen från början igen för att återskapa det lokala tillståndet. Om under den här repetitionsattacker koden försöker anropa en funktion (eller göra andra async work), hållbar uppgift ramverket konsultationer med den *körningshistorik* av den aktuella orchestration. Om den hittar som den [aktivitet funktionen](durable-functions-types-features-overview.md#activity-functions) har redan utförda och gav några resultat, det spelar upp den funktionsresultat och orchestrator-koden fortsätter att köras. Detta fortsätter händer tills Funktionskoden hämtar till en punkt där antingen den är klar eller också har den schemalagda ny asynkron åtgärd.

### <a name="orchestrator-code-constraints"></a>Begränsningar för orchestrator-kod

Beteendet repetitionsattacker skapar begränsningar på vilken typ av kod som kan skrivas i en orchestrator-funktion. Till exempel måste orchestrator-koden vara deterministisk, eftersom den ska återupprepas flera gånger och måste ge samma resultat varje gång. Den fullständiga listan över begränsningar finns i den [Orchestrator kod begränsningar](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) delen av den **kontrollpunkter och starta om** artikeln.

## <a name="language-support"></a>Stöd för språk

För närvarande C# (Functions v1 och v2), F # och JavaScript (endast funktioner v2) är de enda språk som stöds för varaktiga funktioner. Detta inkluderar orchestrator och Aktivitetsfunktioner. Framöver kommer vi lägga till stöd för alla språk som stöds av Azure Functions. Se Azure-funktioner [GitHub-lagringsplatsen Problemlista](https://github.com/Azure/azure-functions-durable-extension/issues) att se senaste status för vår ytterligare språk stöder arbete.

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

Tillägget varaktiga funktioner genererar automatiskt strukturerade spårningsdata till [Application Insights](functions-monitoring.md) när funktionsappen har konfigurerats med en Application Insights-instrumenteringsnyckel. Dessa spårningsdata kan användas för att övervaka förloppet för ditt orkestreringar och beteende.

Här är ett exempel på hur varaktiga funktioner spårning av händelser som ser ut i Application Insights portal med [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![App Insights-frågeresultat](media/durable-functions-overview/app-insights-1.png)

Det är mycket användbara strukturerade data, allt i den `customDimensions` i varje loggpost. Här är ett exempel på en sådan post fullständigt expanderas.

![customDimensions fält i App Insights-fråga](media/durable-functions-overview/app-insights-2.png)

Du kan förvänta dig att se redundant loggposter för uppspelat åtgärder på grund av varaktiga uppgift Framework avsändaren repetitionsattacker beteende. Detta kan vara användbar för att förstå beteendet repetitionsattacker core-motorn. Den [diagnostik](durable-functions-diagnostics.md) artikeln visar exempelfrågor som filtrerar ut repetitionsattacker loggar så att du kan se bara ”i realtid” loggarna.

## <a name="storage-and-scalability"></a>Lagring och skalbarhet

Tillägget varaktiga funktioner använder Azure Storage-köer, tabeller och BLOB-objekt för att bevara körning historik tillstånd och utlöser körning av funktion. Standardkontot för lagring för funktionsappen kan användas eller du kan konfigurera ett separat lagringskonto. Du kanske vill ett separat konto på grund av dataflöde gränser. Orchestrator-koden du skriver behöver inte (och bör inte) interagera med entiteter i dessa lagringskonton. Entiteterna som hanteras direkt av varaktiga uppgift-ramverket som en implementeringsdetalj.

Orchestrator-funktioner schemalägga Aktivitetsfunktioner och få svar via interna Kömeddelanden. När en funktionsapp som körs i Azure Functions-förbrukningsplanen kan dessa köer övervakas av den [Azure Functions skala Controller](functions-scale.md#how-the-consumption-plan-works) och ny beräkning instanser läggs till efter behov. Vid utskalning till flera virtuella datorer kan en orchestrator-funktion körs på en virtuell dator medan den anropar Aktivitetsfunktioner körs på flera olika virtuella datorer. Du hittar mer information i skala beteendet för varaktiga funktioner i [prestanda och skalning](durable-functions-perf-and-scale.md).

Tabellagring används för att lagra körningshistorik för orchestrator-konton. När en instans rehydrates på en viss virtuell dator, hämtar den dess körningshistorik från table storage så att den kan återskapa det lokala tillståndet. Något av de praktiskt att ha historik som är tillgängliga i Table storage är att du kan ta en titt och historik över din orkestreringar med verktyg som [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer skärmbild](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Det är enkelt och praktiskt att se körningshistorik i table storage, Undvik att ta alla beroenden på den här tabellen. Det kan ändras när tillägget varaktiga funktioner utvecklas.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

Alla kända problem som ska spåras i den [GitHub-ärenden](https://github.com/Azure/azure-functions-durable-extension/issues) lista. Om du stöter på problem och kan inte hitta problemet i GitHub, öppnas ett nytt ärende och innehåller en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fortsätt att läsa dokumentationen för varaktiga funktioner](durable-functions-types-features-overview.md)

> [!div class="nextstepaction"]
> [Installera tillägget varaktiga funktioner och -exempel](durable-functions-install.md)

