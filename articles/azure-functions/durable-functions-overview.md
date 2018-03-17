---
title: "Beständiga funktioner översikt – Azure (förhandsversion)"
description: "Introduktion till tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b5269bb51c787c927b4224b3520d5514b6d24501
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="durable-functions-overview-preview"></a>Beständiga översikt över Functions (förhandsgranskning)

*Beständiga funktioner* är en utökning av [Azure Functions](functions-overview.md) och [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) som låter dig skriva tillståndskänslig funktioner i en miljö med serverlösa. Tillägget hanterar tillstånd, kontrollpunkter och startas om för dig.

Tillägget kan du definiera tillståndskänslig arbetsflöden i en ny typ av funktion som kallas en *orchestrator funktionen*. Här följer några av fördelarna med orchestrator-funktioner:

* De definierar arbetsflöden i kod. Inga JSON-scheman eller designers krävs.
* De kan anropa andra funktioner synkront och asynkront. Du kan spara utdata från kallas funktioner för lokala variabler.
* De automatiskt kontrollpunkt framstegen när funktionen väntar. Lokala tillstånd går aldrig förlorad om processen återanvänds eller den virtuella datorn startas om.

> [!NOTE]
> Beständiga funktioner är en förhandsversion och är en avancerad tillägg för Azure-funktioner som inte är lämplig för alla program. Resten av den här artikeln förutsätter att du har en stark förtrogenhet med [Azure Functions](functions-overview.md) begrepp och utmaningarna som deltar i serverlösa programutveckling.

Det primära användningsfallet för beständig funktioner är förenkla komplexa, tillståndskänslig samordning problem i serverlösa program. I följande avsnitt beskrivs några vanliga program mönster som kan dra nytta av beständiga funktioner.

## <a name="pattern-1-function-chaining"></a>Mönstret #1: Funktionen länkning

*Funktionen länkning* refererar till mönstret för att köra en sekvens av funktioner i en viss ordning. Ofta måste resultatet av en funktion tillämpas på indata för en annan funktion.

![Funktionen chaining diagram](media/durable-functions-overview/function-chaining.png)

Beständiga funktioner kan du genomföra det här mönstret koncist i kod.

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

Värdena ”F1”, ”F2”, ”F3” och ”F4” är namnen på andra funktioner i appen funktion. Kontrollflöde implementeras med normal tvingande kodning konstruktioner. Det vill säga kod kör uppifrån och ned och kan omfatta befintliga kontrollen flödet språksemantik, som villkorlig sats och slingor.  Felhantering logik kan ingå i försök/catch/finally-block.

Den `ctx` parameter ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) ger metoder för att anropa andra funktioner efter namn, skicka parametrar och returnera utdata för funktionen. Varje gång koden anropar `await`, beständiga funktioner framework *kontrollpunkter* förloppet för den aktuella instansen för funktionen. Om processen eller VM återvinns halvvägs vid körning, funktionen instans återupptas från den tidigare `await` anropa. Mer information om detta omstartsbeteende senare.

## <a name="pattern-2-fan-outfan-in"></a>Mönstret #2: Fan-in/fan-i

*FAN-in/fan-i* refererar till mönstret för utför flera funktioner parallellt och sedan väntar på att alla ska slutföras.  Ofta arbetet vissa aggregering på resultaten som returnerades från funktionerna.

![FAN-in/fan-i diagrammet](media/durable-functions-overview/fan-out-fan-in.png)

Med normal funktion, fläkt kan du göra genom att låta funktionen Skicka flera meddelanden till en kö. Fläkt tillbaka i är dock mycket mer utmanande. Du skulle behöva skriva kod för att spåra när kön-utlösta funktioner avslutas och lagra funktionen utdata. Tillägget varaktiga funktioner hanterar det här mönstret med relativt enkla kod.

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

Fan-out arbete distribueras till flera instanser av funktionen `F2`, och vad som ska spåras med hjälp av en dynamisk lista över aktiviteter. .NET `Task.WhenAll` API anropas för att vänta tills alla kallas funktioner ska slutföras. Sedan `F2`funktionen matar ut sammanställs från dynamisk uppgiftslistan och skickas till den `F3` funktion.

Automatiska kontrollpunkter som sker på de `await` anropa `Task.WhenAll` säkerställer att alla krasch eller starta om datorn halvvägs via inte kräver en omstart av alla redan slutförts uppgifter.

## <a name="pattern-3-async-http-apis"></a>Mönster för #3: Asynkron http-API: er

Det tredje mönstret handlar om problemet att samordna tillståndet för långvariga åtgärder med externa klienter. Ett vanligt sätt att genomföra det här mönstret är genom att låta tidskrävande åtgärden utlöses av en HTTP-anrop och omdirigering av klienten till en slutpunkt för status som de kan avsöka om du vill veta när åtgärden har slutförts.

![Diagram över HTTP-API](media/durable-functions-overview/async-http-api.png)

Beständiga funktioner innehåller inbyggda API: er som förenklar koden du skriver för att interagera med tidskrävande funktionen körningar. Den [exempel](durable-functions-install.md) visar ett enkelt REST-kommando som kan användas för att starta nya instanser av orchestrator-funktionen. När en instans visar tillägget webhook HTTP APIs att fråga status för orchestrator-funktionen. I följande exempel visas REST-kommandon för att starta en orchestrator och fråga efter dess status. För att göra utelämnas vissa detaljer exemplet.

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

Eftersom tillståndet hanteras av beständiga Functions-runtime, behöver du implementera egna status spårning mekanism.

Även om filnamnstillägget varaktiga funktioner har inbyggda webhooks för att hantera tidskrävande orkestreringarna, du kan implementera det här mönstret själv med hjälp av en egen funktion utlösare (t.ex http-, kö eller Event Hub) och `orchestrationClient` bindning. Du kan exempelvis använda ett kömeddelande för att utlösa avslutning.  Eller så kan du använda en HTTP-utlösare som skyddas av en princip för Azure Active Directory-autentisering i stället för den inbyggda webhooks som använder en genererad nyckel för autentisering. 

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

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametern är ett värde från det `orchestrationClient` utdata bindning, som ingår i tillägget varaktiga funktioner. Det ger metoder för Start, för att skicka händelser till, avbryts och frågar efter nya eller befintliga orchestrator-instanser för funktionen. I exemplet ovan tar en HTTP-funktion som utlöses en `functionName` värde från inkommande URL och överför ett värde till [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Den här bindningen API returnerar ett svar som innehåller en `Location` sidhuvud och ytterligare information om den instans som senare kan användas för att leta upp status för igång instansen eller avsluta.

## <a name="pattern-4-monitoring"></a>Mönstret #4: övervaka

Övervakaren mönstret refererar till en flexibel *återkommande* process i ett arbetsflöde – till exempel avsökning tills vissa villkor är uppfyllda. En vanlig timer-utlösare kan åtgärda ett enkelt scenario, till exempel ett periodiska rensningsjobb men intervallet är statisk och hantera instans livslängd blir komplex. Beständiga funktioner ger flexibla återkommande intervall, aktivitet livstid kan hanteras och möjligheten att skapa flera bildskärmar processer från en enda orchestration.

Ett exempel skulle återföra tidigare asynkrona HTTP API scenariot. I stället för att exponera en slutpunkt för en extern klient kan övervaka en långvarig åtgärd, förbrukar tidskrävande övervakaren en extern slutpunkt väntar på att vissa tillståndsändring.

![Övervakare för diagram](media/durable-functions-overview/monitor.png)

Med hjälp av beständiga funktioner, kan flera bildskärmar som använder valfri slutpunkter skapas i några rader med kod. Övervakarna kan avsluta körningen när vissa villkor uppfylls, eller avslutas med den [DurableOrchestrationClient](durable-functions-instance-management.md), och deras intervall kan ändras baserat på vissa villkor (d.v.s. exponentiell backoff.) Följande kod implementerar en grundläggande Övervakare.

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

När en begäran tas emot, skapa en ny orchestration-instans för detta jobb-ID. Instansen avsöker status tills ett villkor uppfylls och slingan har avslutats. En beständig timer används för att styra avsökningsintervallet. Ytterligare arbete kan utföras eller orchestration kan avsluta. När den `ctx.CurrentUtcDateTime` överskrider den `expiryTime`, övervaka parterna.

## <a name="pattern-5-human-interaction"></a>Mönster för #5: Mänsklig interaktion

Många processer innebära någon typ av mänsklig interaktion. Komplicerade sakerna som rör människor i en automatiserad process är att personer som inte är alltid med hög tillgänglighet och svarstid som molntjänster. Automatiserade processer måste tillåta detta och de göra ofta det med tidsgränser och ersättning logik.

Ett exempel på en affärsprocess som inbegriper mänsklig interaktion är en godkännandeprocess. Godkännande från en chef kan till exempel krävas för en kostnad som överskrider ett visst värde. Om du inte godkänner chef inom 72 timmar (kanske de gick på semester), aktiveras en process för eskalering få godkännande från någon annan (kanske hanterarens manager).

![Mänsklig interaktionsdiagram](media/durable-functions-overview/approval.png)

Det här mönstret kan implementeras med hjälp av en orchestrator-funktion. Orchestrator använder en [varaktiga timer](durable-functions-timers.md) att begära godkännande och eskalera vid timeout. Det skulle vänta tills en [extern händelse](durable-functions-external-events.md), vilket är meddelande som genereras av vissa mänsklig interaktion.

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

Beständiga timern har skapats genom att anropa `ctx.CreateTimer`. Meddelandet tas emot av `ctx.WaitForExternalEvent`. Och `Task.WhenAny` anropas för att bestämma om du vill eskalera (timeout inträffar först) eller bearbeta godkännande (godkännande tas emot innan timeout).

## <a name="the-technology"></a>Tekniken

I bakgrunden tillägget varaktiga funktioner är byggt ovanpå det [beständiga aktiviteten Framework](https://github.com/Azure/durabletask), ett bibliotek med öppen källkod på GitHub för att skapa beständiga aktiviteten orkestreringarna. Hur är Azure Functions serverlösa utvecklingen av Azure WebJobs, är beständiga funktioner liksom serverlösa utvecklingen av beständiga aktiviteten Framework. Beständiga aktiviteten Framework används kraftigt inom Microsoft och utanför samt att automatisera verksamhetskritiska processer. Det är en fysisk anpassning för serverlösa Azure Functions-miljön.

### <a name="event-sourcing-checkpointing-and-replay"></a>Händelsen källa, kontrollpunkter och omsändning

Orchestrator-funktioner på ett tillförlitligt sätt underhålla sina körningstillstånd med hjälp av en molndesignmönstret kallas [händelse källa](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Istället för att lagra direkt den *aktuella* tillståndet för en orchestration varaktiga tillägget använder en endast append-Arkiv för att registrera den *fullständig serie åtgärder* genom funktionen orchestration. Detta har många fördelar, inklusive förbättrar prestanda, skalbarhet och tillgänglighet jämfört med ”dumpning” fullständig runtime-tillståndet. Andra fördelar är att tillhandahålla slutliga konsekvensen för transaktionsdata och underhålla fullständig granskningshistorik och historik. Granskningshistoriken själva Aktivera tillförlitliga kompenserande åtgärder.

Användning av händelse ursprung med det här tillägget är transparent. Under försättsbladen, den `await` operator i en orchestrator-funktion ger kontroll över orchestrator-tråd tillbaka till den beständiga aktiviteten Framework dispatcher. Dispatcher sedan genomför alla nya åtgärder som funktionen orchestrator schemalagda (till exempel anropar en eller flera underordnade funktioner eller schemalägga en beständig timer) till lagring. Den här transparent commit-åtgärden läggs till i *körningstiden* av orchestration-instansen. Historiken lagras i en tabell för lagring. Commit-åtgärden sedan lägger till meddelanden till en kö för att schemalägga det faktiska arbetet. Funktionen orchestrator kan nu vara från minnet. Fakturering för det avbryts om du använder Azure Functions förbrukning planera.  När det är mer arbete att göra, funktionen startas och dess tillstånd rekonstrueras.

När en orchestration-funktion får mer arbete att göra (till exempel ett svarsmeddelande tas emot eller en beständig timer upphör att gälla) orchestrator väcker igen och kör funktionen hel från början igen för att återskapa lokala tillstånd. Om under den här replay koden försöker anropa en funktion (eller göra andra asynkrona arbete), beständiga aktiviteten Framework tittar med den *körningstiden* för den aktuella orchestration. Om den hittar som aktiviteten funktionen redan har körts och gav några resultat den spelar upp som funktionsresultat orchestrator-koden fortsätter att köras. Detta fortsätter tills Funktionskoden kommer till en plats där den är klar eller har schemalagt nya asynkrona arbete som händer.

### <a name="orchestrator-code-constraints"></a>Begränsningar för orchestrator-kod

Beteendet replay skapar begränsningar på vilken typ av kod som kan skrivas i ett orchestrator-funktionen. Till exempel måste orchestrator-koden vara deterministisk, som den ska återupprepas flera gånger och måste ge samma resultat varje gång. En fullständig lista över begränsningar finns i den [Orchestrator kod begränsningar](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) avsnitt i den **kontrollpunkter och starta om** artikel.

## <a name="language-support"></a>Språkstöd

För närvarande är C# det enda språket som stöds för beständig funktioner. Detta inkluderar orchestrator-funktioner och funktioner för aktiviteten. I framtiden kommer vi lägga till stöd för alla språk som stöds av Azure Functions. Se Azure Functions [GitHub-lagringsplatsen problem listan](https://github.com/Azure/azure-functions-durable-extension/issues) att visa den senaste statusen för våra ytterligare språk stöder arbete.

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

Tillägget varaktiga funktioner genererar automatiskt strukturerade spårningsdata till [Programinsikter](functions-monitoring.md) när funktionen appen är konfigurerad med en nyckel för Application Insights instrumentation. Dessa spårningsdata kan användas för att övervaka förloppet för din orkestreringarna och beteende.

Här är ett exempel på hur funktionerna varaktiga spårningshändelser se ut i Application Insights portal med [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Frågeresultat för App Insights](media/durable-functions-overview/app-insights-1.png)

Det är mycket användbar strukturerade data allt i den `customDimensions` i varje loggpost. Här är ett exempel på en sådan post visas.

![customDimensions fält i App Insights fråga](media/durable-functions-overview/app-insights-2.png)

På grund av replay-beteendet för beständiga aktiviteten Framework dispatcher du förväntar dig att se redundant loggposter för upprepat åtgärder. Detta kan vara användbar för att förstå beteendet replay core-motorn. Den [diagnostik](durable-functions-diagnostics.md) artikeln visar exempelfrågor som filtrerar ut replay-loggarna så att du kan se precis ”realtid” loggarna.

## <a name="storage-and-scalability"></a>Lagring och skalbarhet

Tillägget varaktiga funktioner använder Azure Storage-köer och tabeller BLOB för att bevara körning tidigare tillstånd och utlösa funktionen körning. Standardkontot för lagring för funktionen appen kan användas eller konfigurera ett separat lagringskonto. Du kanske vill ett särskilt konto på grund av Lagringsgränser genomflöde. Orchestrator-koden du skriver inte behöver (och bör inte) interagera med entiteter i dessa lagringskonton. Enheterna hanteras direkt av beständiga aktiviteten ramen som en implementering detaljer.

Orchestrator-funktioner Schemalägga aktivitet funktioner och få svar via interna Kömeddelanden. När en funktionsapp körs i Azure Functions förbrukning planen köerna övervakas av den [Azure Functions skala Controller](functions-scale.md#how-the-consumption-plan-works) och nya compute-instanser läggs efter behov. När skalas ut till flera virtuella datorer, kan en orchestrator-funktionen köras på en virtuell dator medan aktiviteten funktioner anropas körs på flera olika virtuella datorer. Du hittar mer information på skalan beteendet för beständig funktioner i [prestanda och skalning](durable-functions-perf-and-scale.md).

Tabellagring används för att lagra körningstiden för orchestrator-konton. När en instans rehydrates på en viss virtuell dator, hämtar den dess körningstiden från tabellagring så att den kan återskapa dess lokala tillstånd. Något av det praktiskt att historik som är tillgängliga i Table storage är att du kan ta en titt och se historiken för din orkestreringarna med hjälp av verktyg som [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Lagringsutforskaren skärmbild](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Det är enkelt och bekvämt att se körningstiden i table storage, Undvik att ta med eventuella beroende på den här tabellen. Den kan ändras när tillägget varaktiga funktioner utvecklas.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

I allmänhet alla kända problem ska spåras i den [GitHub problem](https://github.com/Azure/azure-functions-durable-extension/issues) lista. Om du stöter på problem och går inte att hitta problemet i GitHub, öppnas ett nytt ärende och innehåller en detaljerad beskrivning av problemet. Även om du vill ställa en fråga, passa på att öppna ett GitHub-problem och tagga som en fråga.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fortsätta att läsa dokumentationen varaktiga funktioner](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Installera tillägget beständiga funktioner och exempel](durable-functions-install.md)

