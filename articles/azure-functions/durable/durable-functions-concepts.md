---
title: Varaktiga funktioner mönster och tekniska begrepp i Azure Functions
description: Lär dig hur tillägget varaktiga funktioner i Azure Functions ger dig fördelarna med tillståndskänsliga kodkörning i molnet.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: aa9563266f6b43e3bc2f21fbc0b340c86c5895ae
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608739"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Varaktiga funktioner mönster och tekniska begrepp (Azure Functions)

Varaktiga funktioner är en utökning av [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Du kan använda varaktiga funktioner för att skriva tillståndskänsliga funktioner i en serverfri miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. 

Den här artikeln får du detaljerad information om beteenden för tillägget varaktiga funktioner för Azure Functions och vanliga mönster för implementering. Informationen kan hjälpa dig att avgöra hur du använder varaktiga funktioner för att lösa dina utmaningar för utveckling.

> [!NOTE]
> Varaktiga funktioner är en avancerad tillägg för Azure Functions som inte är lämplig för alla program. Den här artikeln förutsätter att du har en stark bekant med begrepp i [Azure Functions](../functions-overview.md) och utmaningarna som ingår i program utan Server-utveckling.

## <a name="patterns"></a>Mönster

Det här avsnittet beskrivs några vanliga mönster där varaktiga funktioner kan vara användbara.

### <a name="chaining"></a>Mönster #1: Funktionslänkning

I funktionen länkning mönster, utför en serie funktioner i en viss ordning. I det här mönstret används utdata för en funktion till indata för en annan funktion.

![Ett diagram över funktionen länkning mönster](./media/durable-functions-concepts/function-chaining.png)

Du kan använda varaktiga funktioner för att implementera funktionen länkning användningsmönstret koncist som visas i följande exempel:

#### <a name="c-script"></a>C#-skript

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Det finns vissa skillnader mellan en förkompilerade varaktiga funktion C# och skriva en förkompilerade varaktiga funktion den C# skript som visas i exemplet. I en C# förkompilerad version funktion, hållbar parametrar måste vara dekorerad med respektive attribut. Ett exempel är den `[OrchestrationTrigger]` attributet för den `DurableOrchestrationContext` parametern. I en C# förkompilerad version varaktiga funktionen om parametrarna är inte korrekt dekorerad körningen det går inte att mata in variablerna i funktionen och ett fel inträffar. Fler exempel finns i den [azure-functions-durable-extension exemplen på GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

I det här exemplet värdena `F1`, `F2`, `F3`, och `F4` är namnen på andra funktioner i funktionsappen. Du kan implementera Kontrollflöde med hjälp av kodning konstruktioner som normalt krävs. Koden utför uppifrån ned. Koden kan omfatta befintliga språk control flow semantik, som villkor och loopar. Du kan inkludera felhanteringslogik i `try` / `catch` / `finally` block.

Du kan använda den `context` parametern [DurableOrchestrationContext] \(.NET\) och `context.df` objekt (JavaScript) för att anropa andra funktioner efter namn, skicka parametrar och returnerar funktionen utdata. Varje gång koden anropar `await` (C#) eller `yield` (JavaScript), varaktiga funktioner framework kontrollpunkter förloppet för den aktuella funktion-instansen. Om processen eller VM återvinns halvvägs körningen, funktionen instans återgår från föregående `await` eller `yield` anropa. Mer information finns i nästa avsnitt, mönstret nr 2: Fläkt out/samlande.

> [!NOTE]
> Den `context` i JavaScript-objektet representerar hela [funktionen kontext](../functions-reference-node.md#context-object), inte bara den [DurableOrchestrationContext] parametern.

### <a name="fan-in-out"></a>Mönster #2: Fläkt out/samlande

I fläkten out/samlande mönster kan du köra flera funktioner parallellt och väntar du tills alla funktioner ska slutföras. Vissa aggregering arbete utförs ofta i resultaten som returneras från funktioner.

![Ett diagram över fläkten out/fläkt mönster](./media/durable-functions-concepts/fan-out-fan-in.png)

Med normal funktion, kan du sprida ut genom att använda funktionen Skicka flera meddelanden till en kö. Fläkt i är mycket mer utmanande. För att sprida, i en normal funktion kan du skriva kod för att spåra när kön-utlösta funktioner slutet och sedan lagra fungera utdata. 

Tillägget varaktiga funktioner hanterar det här mönstret med relativt enkel kod:

#### <a name="c-script"></a>C#-skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Fan-out arbetet ska distribueras till flera instanser av den `F2` funktion. Arbetet spåras med hjälp av en dynamisk lista över aktiviteter. .NET `Task.WhenAll` API eller JavaScript `context.df.Task.all` API anropas för att vänta tills de anropade funktionerna ska slutföras. Sedan den `F2` funktionen matar ut aggregeras i dynamiska uppgiftslistan och skickas till den `F3` funktion.

Automatiska kontrollpunkter som sker på den `await` eller `yield` anropa `Task.WhenAll` eller `context.df.Task.all` säkerställer att en potentiell mitt krasch eller starta om datorn inte kräver att starta om en redan slutförd aktivitet.

### <a name="async-http"></a>Mönster #3: Async HTTP API: er

Async HTTP APIs mönstret löser problemet med att samordna tillståndet för långvariga åtgärder med externa klienter. Ett vanligt sätt att implementera det här mönstret är att låta en HTTP och anropa utlösare tidskrävande åtgärd. Sedan kan dirigera om klienten till en slutpunkt för status som klienten frågar om du vill veta när åtgärden är klar.

![Ett diagram över HTTP-API-mönster](./media/durable-functions-concepts/async-http-api.png)

Varaktiga funktioner innehåller inbyggda API: er som förenklar koden du skriver så att interagera med tidskrävande funktionskörningar. Snabbstart-exempel varaktiga funktioner ([ C# ](durable-functions-create-first-csharp.md) och [JavaScript](quickstart-js-vscode.md)) visar ett enkelt REST-kommando som du kan använda för att starta nya instanser av orchestrator-funktion. När en instans har startat exponerar tillägget webhook HTTP APIs som fråga status för orchestrator-funktion. 

I följande exempel visas REST-kommandon som startar en orchestrator och fråga efter dess status. För tydlighetens skull utelämnas vissa detaljer i exemplet.

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

Eftersom varaktiga funktioner runtime hanterar tillstånd, behöver du inte implementera en egen mekanism för status-spårning.

Tillägget varaktiga funktioner har inbyggda webhooks som hanterar långvariga orkestreringar. Du kan också implementera detta mönster genom att använda din egen function-utlösare (t.ex HTTP, en kö eller Azure Event Hubs) och `orchestrationClient` bindning. Du kan exempelvis använda ett kömeddelande för att utlösa uppsägning. Eller så kan du använda en HTTP-utlösare som skyddas av en princip för Azure Active Directory-autentisering i stället för den inbyggda webhooks som använder en genererad nyckel för autentisering.

Här följer några exempel på hur du använder mönstret HTTP-API:

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> När du utvecklar lokalt i JavaScript för att använda metoder på `DurableOrchestrationClient`, måste du ange miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>` (till exempel `localhost:7071`). Mer information om det här kravet finns i [GitHub-ärende 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

I .NET, den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametern är ett värde från den `orchestrationClient` utdata bindning, som ingår i tillägget varaktiga funktioner. I JavaScript, returneras det här objektet genom att anropa `df.getClient(context)`. De här objekten tillhandahåller metoder som du kan använda för att starta, skicka händelser till, avsluta och fråga för nya eller befintliga orchestrator-funktion-instanser.

I föregående exempel, tar en HTTP-utlöst funktion en `functionName` värdet från inkommande URL och skickar värdet som ska [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Den [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) bindning API sedan returnerar ett svar som innehåller en `Location` rubrik och ytterligare information om instansen. Du kan använda informationen senare att leta upp status för igång instansen eller att avsluta instansen.

### <a name="monitoring"></a>Mönster #4: Övervaka

Mönstret övervakaren refererar till en flexibel, återkommande process i ett arbetsflöde. Ett exempel avsökning tills särskilda villkor uppfylls. Du kan använda en vanlig [timerutlösare](../functions-bindings-timer.md) för att åtgärda en grundläggande scenario, till exempel en regelbunden rensningsjobb, men intervallet är statisk och hantera instans livslängd blir komplex. Du kan använda varaktiga funktioner för att skapa flexibla återkommande intervall, hantera uppgiften livslängd och skapa flera Övervakare processer från en enda dirigering.

Ett exempel på mönstret övervakaren är att omvända det tidigare async HTTP API-scenariot. I stället för att exponera en slutpunkt för en extern klient kan övervaka en tidskrävande åtgärd kan övervakaren tidskrävande förbrukar en extern slutpunkt och väntar sedan en tillståndsändring.

![Ett diagram över mönstret Övervakare](./media/durable-functions-concepts/monitor.png)

Du kan använda varaktiga funktioner för att skapa flera bildskärmar som använder godtyckliga slutpunkter i några få kodrader. Övervakare kan avsluta körningen när ett villkor uppfylls, eller [DurableOrchestrationClient](durable-functions-instance-management.md) kan avsluta övervakarna. Du kan ändra en Övervakare `wait` intervall baserat på ett specifikt villkor (till exempel exponentiell backoff.) 

Följande kod implementerar en grundläggande Övervakare:

#### <a name="c-script"></a>C#-skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

När en begäran tas emot, skapas en ny orchestration-instans för den jobb-ID. Instansen avsöker status tills ett villkor uppfylls och slingan har avslutats. En hållbar timer styr avsökningsintervallet. Sedan kan du utföra mer arbete eller dirigering kan avsluta. När den `context.CurrentUtcDateTime` (.NET) eller `context.df.currentUtcDateTime` (JavaScript) överskrider den `expiryTime` värde, det Övervakare upphört att gälla.

### <a name="human"></a>Mönster #5: Mänsklig interaktion

Många automatiserade processer involverar någon form av mänsklig interaktion. Det är svårt att som rör människor i en automatiserad process eftersom personer som inte är med hög tillgänglighet och som svarar med molntjänster. En automatiserad process kan göra att detta med hjälp av timeout och kompensation logik.

En godkännandeprocess är ett exempel på en affärsprocess som inbegriper mänsklig interaktion. Godkännande från en chef kan krävas för en utgiftsrapport som överskrider ett visst belopp för dollar. Om manager inte godkänna utgiftsrapporten inom 72 timmar (kanske manager gick på semester), startar en process för eskalering att få godkännande från någon annan (kanske chefens chef).

![Ett diagram över mönstret mänsklig interaktion](./media/durable-functions-concepts/approval.png)

Du kan implementera mönstret i det här exemplet med hjälp av en orchestrator-funktion. Orchestrator använder en [varaktiga timer](durable-functions-timers.md) till begäran om godkännande. Orchestrator eskalerar om timeout uppstår. Orchestrator väntar en [extern händelse](durable-functions-external-events.md), till exempel ett meddelande som genereras av en mänsklig interaktion.

Följande exempel skapar en godkännandeprocess för att demonstrera mänsklig interaktionsmönstret:

#### <a name="c-script"></a>C#-skript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Om du vill skapa varaktiga timern anropa `context.CreateTimer` (.NET) eller `context.df.createTimer` (JavaScript). Meddelandet tas emot av `context.WaitForExternalEvent` (.NET) eller `context.df.waitForExternalEvent` (JavaScript). Sedan `Task.WhenAny` (.NET) eller `context.df.Task.any` (JavaScript) anropas för att bestämma om du vill att eskalera (tidsgräns sker först) eller bearbeta godkännandet (godkännandet tas emot innan timeout).

En extern klient kan leverera händelseaviseringen till en funktion för att vänta på orchestrator genom att använda antingen den [inbyggda HTTP APIs](durable-functions-http-api.md#raise-event) eller genom att använda den [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) -API en annan funktion:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>Tekniken

I bakgrunden tillägget varaktiga funktioner är byggt ovanpå den [varaktiga uppgift Framework](https://github.com/Azure/durabletask), ett bibliotek för öppen källkod på GitHub som används för att skapa varaktiga uppgift orkestreringar. Som Azure Functions är en serverlös utveckling av Azure WebJobs, är varaktiga funktioner utan Server utvecklingen av varaktiga uppgift Framework. Microsoft och andra organisationer som använder du varaktiga uppgift Framework stor utsträckning för att automatisera verksamhetskritiska processer. Det är därför lämplig för serverlösa Azure Functions-miljön.

### <a name="event-sourcing-checkpointing-and-replay"></a>Händelsekällor kontrollpunkter och återuppspelning

Orchestrator-funktioner på ett tillförlitligt sätt underhålla sina körningstillstånd med hjälp av den [händelsekällor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) designmönstret. I stället för att direkt spara det aktuella tillståndet för en orkestrering, använder en lagringsplats för endast tillägg i tillägget varaktiga funktioner för att registrera alla åtgärder tar funktionen orchestration. En lagringsplats för endast tillägg har många fördelar jämfört med ”dumpning” fullständig runtime-tillståndet. Fördelar ökad prestanda, skalbarhet och tillgänglighet. Du kan även få slutlig konsekvens för transaktionsdata och fullständig granskningshistorik och historik. Granskningshistoriken stöder tillförlitliga kompenserande åtgärder.

Varaktiga funktioner använder händelsekällor transparent. I bakgrunden i `await` (C#) eller `yield` (JavaScript) operator i en orchestrator-funktion ger kontroll över orchestrator-tråd tillbaka till varaktiga uppgift Framework-avsändaren. Avsändaren sedan genomför alla nya åtgärder som orchestrator-funktion som schemalagts (till exempel anropa en eller flera underordnade funktioner eller schemalägga en hållbar timer) till lagring. Transparent commit-åtgärden läggs till körningshistorik för orchestration-instans. Historiken lagras i en lagringstabell. Commit-åtgärden sedan lägger till meddelanden i kö för att schemalägga det faktiska arbetet. Orchestrator-funktion kan nu tas bort från minnet. 

Faktureringen för orchestrator-funktion stoppas om du använder Azure Functions-förbrukningsplanen. När det finns mer arbete att göra, startar om funktionen och dess tillstånd rekonstruerad.

När en orchestration-funktion får mer arbete att göra (till exempel ett svarsmeddelande tas emot eller en hållbar timern upphör) orchestrator väckningspaketet och igen kör hela funktionen från början till återskapa lokala tillstånd. 

Under repetitionsattacker, om koden försöker anropa en funktion (eller göra andra async work), hållbar uppgift ramverket konsultationer körningstiden för den aktuella orchestration. Om den hittar som den [aktivitet funktionen](durable-functions-types-features-overview.md#activity-functions) har redan körts och gav ett resultat, det spelar upp den funktionsresultat och orchestrator-koden fortsätter att köras. Återuppspelning fortsätter tills Funktionskoden är klar eller tills den är schemalagd ny asynkron åtgärd.

### <a name="orchestrator-code-constraints"></a>Begränsningar för orchestrator-kod

Återuppspelning beteendet för orchestrator kod skapar begränsningar på vilken typ av kod som du kan skriva i en orchestrator-funktion. Orchestrator-koden måste till exempel vara deterministisk eftersom det ska återupprepas flera gånger, och den måste ge samma resultat varje gång. Läs den fullständiga listan med begränsningar [Orchestrator kod begränsningar](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

Tillägget varaktiga funktioner genererar automatiskt strukturerade spårningsdata till [Application Insights](../functions-monitoring.md) om du ställer in din funktionsapp med en Azure Application Insights-instrumenteringsnyckel. Du kan använda spårning av data för att övervaka åtgärder och förloppet för ditt orkestreringar.

Här är ett exempel på hur varaktiga funktioner spårning av händelser ser ut i Application Insights-portalen när du använder [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Frågeresultat för Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Du kan hitta användbara strukturerade data i den `customDimensions` i varje loggpost. Här är ett exempel på en post som är fullständigt expanderas:

![Fältet customDimensions i en Application Insights-fråga](./media/durable-functions-concepts/app-insights-2.png)

Du kan förvänta dig att se redundant loggposter för uppspelat åtgärder på grund av varaktiga uppgift Framework avsändaren repetitionsattacker beteende. Redundant loggposter kan hjälpa dig att förstå hur kärnmotor repetitionsattacker. Den [diagnostik](durable-functions-diagnostics.md) artikeln visar exempelfrågor som filtrerar ut repetitionsattacker loggar så att du kan se bara ”i realtid” loggarna.

## <a name="storage-and-scalability"></a>Lagring och skalbarhet

Köer, tabeller och blobbar använder i Azure Storage tillägget varaktiga funktioner för att bevara körning historik tillstånd och utlöser körning av funktion. Du kan använda standardkontot för lagring för funktionsappen eller du kan konfigurera ett separat lagringskonto. Du kanske vill ett separat konto baserat på dataflöde gränser. Orchestrator-koden du skriver interagera inte med entiteter i dessa lagringskonton. Hållbar uppgift ramverket hanterar entiteterna direkt som en implementeringsdetalj.

Orchestrator-funktioner schemalägga Aktivitetsfunktioner och få svar via interna Kömeddelanden. När en funktionsapp som körs i Azure Functions-förbrukningsplanen, den [Azure Functions skala controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) övervakar dessa köer. Nya instanser läggs efter behov. Vid utskalning till flera virtuella datorer, köra en orchestrator-funktion på en virtuell dator när Aktivitetsfunktioner som orchestrator-funktionsanrop som kan köras på flera olika virtuella datorer. Mer information om beteendet skala varaktiga funktioner finns i [prestanda och skalning](durable-functions-perf-and-scale.md).

Körningstiden för orchestrator-konton lagras i table storage. När en instans rehydrates på en viss virtuell dator, hämtar orchestrator dess körningshistorik från table storage så att den kan återskapa det lokala tillståndet. En praktisk aspekt av historik som är tillgängliga i table storage är att du kan använda verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) att se historiken för din orkestreringar.

Lagringsblobar används främst som en leasingmekanism för att koordinera skalbar orchestration instanser mellan flera virtuella datorer. Lagringsblobar innehåller data för stora meddelanden som inte kan lagras direkt i tabeller eller köer.

![En skärmbild av Azure Storage Explorer](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Även om det är enkelt och praktiskt att se körningshistorik i table storage, gör eventuella beroenden på den här tabellen. Tabellen ändras när tillägget varaktiga funktioner utvecklas.

## <a name="known-issues"></a>Kända problem

Alla kända problem som ska spåras i den [GitHub-ärenden](https://github.com/Azure/azure-functions-durable-extension/issues) lista. Om du stöter på problem och det går inte att hitta problemet i GitHub kan du öppna ett nytt ärende. Innehåller en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

Läs mer om varaktiga funktioner i [varaktiga funktioner fungerar typer och funktioner](durable-functions-types-features-overview.md). 

Så här kommer du igång:

> [!div class="nextstepaction"]
> [Skapa din första varaktiga funktion](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
