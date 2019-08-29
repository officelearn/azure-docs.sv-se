---
title: Durable Functions mönster och tekniska begrepp i Azure Functions
description: Lär dig hur Durable Functions tillägget i Azure Functions ger dig fördelarna med tillstånds känslig kod körning i molnet.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 828bcaa8c93454ba845c30c03c76144310891123
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098258"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions mönster och tekniska begrepp (Azure Functions)

Durable Functions är en utökning av [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Du kan använda Durable Functions för att skriva tillstånds känsliga funktioner i en miljö utan server. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. 

Den här artikeln innehåller detaljerad information om funktionerna i Durable Functions-tillägget för Azure Functions och vanliga implementerings mönster. Informationen kan hjälpa dig att avgöra hur du ska använda Durable Functions för att hjälpa till att lösa dina utvecklings utmaningar.

> [!NOTE]
> Durable Functions är ett avancerat tillägg för Azure Functions som inte passar för alla program. Den här artikeln förutsätter att du har en stark välbekanthet med koncept i [Azure Functions](../functions-overview.md) och de utmaningar som är inblandade i program utveckling utan server.

## <a name="patterns"></a>Mönster

I det här avsnittet beskrivs några vanliga program mönster där Durable Functions kan vara användbart.

### <a name="chaining"></a>Mönster #1: Funktionslänkning

I funktions kedje mönstret körs en sekvens med funktioner i en speciell ordning. I det här mönstret tillämpas utdata från en funktion på indatan för en annan funktion.

![Ett diagram över funktions kedje mönstret](./media/durable-functions-concepts/function-chaining.png)

Du kan använda Durable Functions för att implementera funktions länknings mönstret på ett koncist sätt som visas i följande exempel:

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
> Det finns små skillnader mellan att C# C# skriva en förkompilerad varaktig funktion i och skriva en förkompilerad, hållbar funktion i skriptet som visas i exemplet. I en C# förkompilerad funktion måste de varaktiga parametrarna dekoreras med respektive attribut. Ett exempel är `[OrchestrationTrigger]` attributet `DurableOrchestrationContext` för parametern. I en C# förkompilerad varaktig funktion, om parametrarna inte är korrekt dekorerade, kan inte körningen mata in variablerna i funktionen och ett fel uppstår. Fler exempel finns i exemplen [Azure-Functions-hållbart-extensions på GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

I det här exemplet är värdena `F1` `F3`, `F2`, och `F4` namnen på andra funktioner i Function-appen. Du kan implementera kontroll flödet med hjälp av vanliga kodnings konstruktioner. Koden körs uppifrån och ned. Koden kan omfatta befintliga semantiska språk kontroll flöde, t. ex. villkor och slingor. Du kan inkludera fel hanterings logik `try` i / / `catch` `finally` block.

Du kan `context` använda parametern [DurableOrchestrationContext] \(.net\) och `context.df` objektet (Java Script) för att anropa andra funktioner efter namn, pass parametrar och returnera funktions resultat. Varje gång koden anropar `await` (C#) eller `yield` (Java Script) visar Durable Functions Framework förloppet för den aktuella funktions instansen. Om processen eller den virtuella datorn återvinner mitt i körningen fortsätter funktions instansen från föregående `await` eller `yield` anropa. Mer information finns i nästa avsnitt, mönster #2: Fläkt ut/fläkt i.

> [!NOTE]
> Objektet i Java Script representerar hela [funktions kontexten](../functions-reference-node.md#context-object), inte bara parametern [DurableOrchestrationContext] `context`

### <a name="fan-in-out"></a>Mönster #2: Fläkt ut/fläkt i

I mönstret fläkt ut/fläkt i, kör du flera funktioner parallellt och väntar sedan på att alla funktioner ska slutföras. Ofta utförs en del sammansättnings arbete på resultaten som returneras från funktionerna.

![Ett diagram över fläkt-och fläkt mönstret](./media/durable-functions-concepts/fan-out-fan-in.png)

Med normala funktioner kan du använda funktionen Skicka flera meddelanden till en kö. Fanning tillbaka i är mycket mer utmanande. I en normal funktion skriver du kod för att spåra när funktionen för köade funktioner har slutförts och sedan lagra funktions resultat. 

Durable Functions-tillägget hanterar det här mönstret med relativt enkel kod:

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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

Fläkt arbetet distribueras till flera instanser av `F2` funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. .Net `Task.WhenAll` API eller Java Script `context.df.Task.all` API anropas för att vänta tills alla anropade funktioner har slutförts. Sedan aggregeras `F3`funktionenutdata från den dynamiska uppgifts listan och skickas till funktionen. `F2`

Den automatiska kontroll punkten som sker `await` vid eller `yield` anropar `Task.WhenAll` eller `context.df.Task.all` säkerställer att en eventuell halvvägs krasch eller omstart kräver att en redan slutförd aktivitet startas om.

### <a name="async-http"></a>Mönster #3: Asynkrona HTTP API: er

Det asynkrona API-mönstret för HTTP löser problemet med att koordinera tillstånden för långvariga åtgärder med externa klienter. Ett vanligt sätt att implementera det här mönstret är genom att låta ett HTTP-anrop utlösa den långvariga åtgärden. Omdirigera sedan klienten till en status slut punkt som klienten avsöker för att lära sig när åtgärden har slutförts.

![Ett diagram över HTTP API-mönstret](./media/durable-functions-concepts/async-http-api.png)

Durable Functions innehåller inbyggda API: er som fören klar koden som du skriver för att interagera med tids krävande funktions körningar. Durable Functions snabb starts exempel[C#](durable-functions-create-first-csharp.md) (och [Java Script](quickstart-js-vscode.md)) visar ett enkelt rest-kommando som du kan använda för att starta nya Orchestrator-funktioner. När en instans startar visar tillägget webhook HTTP-API: er som frågar Orchestrator-funktionens status. 

I följande exempel visas REST-kommandon för att starta en Orchestrator och fråga dess status. För tydlighetens skull utelämnas viss information från exemplet.

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

Eftersom Durable Functions runtime hanterar tillstånd behöver du inte implementera din egen status spårnings funktion.

Durable Functions tillägget har inbyggda Webhooks som hanterar långvariga dirigeringar. Du kan implementera det här mönstret själv genom att använda dina egna funktions utlösare (till exempel http, en kö eller Azure Event Hubs `orchestrationClient` ) och bindningen. Du kan till exempel använda ett Queue-meddelande för att utlösa avslutning. Eller så kan du använda en HTTP-utlösare som skyddas av en Azure Active Directory autentiseringsprincip i stället för de inbyggda webhookarna som använder en genererad nyckel för autentisering.

Här följer några exempel på hur du använder HTTP API-mönstret:

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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

I .net är parametern [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` `orchestrationClient` ett värde från utgående bindning, som är en del av Durable Functions-tillägget. I Java Script returneras det här objektet genom att `df.getClient(context)`anropa. De här objekten ger metoder som du kan använda för att starta, skicka händelser till, avsluta och fråga efter nya eller befintliga Orchestrator-funktioner.

I föregående exempel tar en http-utlöst funktion i ett `functionName` värde från den inkommande URL: en och skickar värdet till [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) -bindnings-API: et returnerar sedan ett `Location` svar som innehåller ett sidhuvud och ytterligare information om instansen. Du kan använda informationen senare för att leta upp statusen för den startade instansen eller avsluta instansen.

### <a name="monitoring"></a>Mönster #4: Övervaka

Övervaknings mönstret avser en flexibel, återkommande process i ett arbets flöde. Ett exempel är att avsöker tills vissa villkor är uppfyllda. Du kan använda en vanlig [timer](../functions-bindings-timer.md) -utlösare för att hantera ett grundläggande scenario, till exempel ett periodiskt rensnings jobb, men dess intervall är statiskt och hanteringen av instans livstider blir komplex. Du kan använda Durable Functions för att skapa flexibla upprepnings intervall, hantera aktivitets livs längder och skapa flera övervaknings processer från ett enda dirigering.

Ett exempel på ett övervaknings mönster är att byta till det tidigare asynkrona HTTP API-scenariot. I stället för att exponera en slut punkt för en extern klient för att övervaka en långvarig åtgärd, förbrukar den tids krävande övervakaren en extern slut punkt och väntar sedan på en tillstånds ändring.

![Ett diagram över övervaknings mönstret](./media/durable-functions-concepts/monitor.png)

I några få kodrader kan du använda Durable Functions för att skapa flera Övervakare som ser godtyckliga slut punkter. Övervakarna kan avsluta körningen när ett villkor är uppfyllt, eller så kan [DurableOrchestrationClient](durable-functions-instance-management.md) avsluta övervakarna. Du kan ändra en övervakares `wait` intervall baserat på ett speciellt villkor (till exempel exponentiell backoff.) 

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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

När en begäran tas emot skapas en ny Dirigerings instans för jobb-ID: t. Instansen avsöker en status tills ett villkor uppfylls och loopen avslutas. En varaktig timer styr avsöknings intervallet. Sedan kan du utföra mer arbete, eller så kan dirigeringen avslutas. När (.net) eller `context.df.currentUtcDateTime` `expiryTime` (Java Script) överskrider värdet, avslutas övervakaren. `context.CurrentUtcDateTime`

### <a name="human"></a>Mönster #5: Mänsklig interaktion

Många automatiserade processer omfattar viss typ av mänsklig interaktion. Att involvera människor i en automatiserad process är knepigt eftersom människor inte är hög tillgängliga och svarar som moln tjänster. En automatiserad process kan tillåta detta genom att använda tids gränser och kompensations logik.

En godkännande process är ett exempel på en affärs process som involverar mänsklig interaktion. Godkännande från en chef kan krävas för en utgifts rapport som överstiger en viss rabatt. Om chefen inte godkänner utgifts rapporten inom 72 timmar (kanske chefen gick på semestern), används en eskalering för att få godkännande från någon annan (kanske chefens chef).

![Ett diagram över mänsklig interaktions mönster](./media/durable-functions-concepts/approval.png)

Du kan implementera mönstret i det här exemplet med hjälp av en Orchestrator-funktion. Orchestrator använder en [varaktig timer](durable-functions-timers.md) för att begära godkännande. Orchestrator eskalerar om timeout inträffar. Orchestrator väntar på en [extern händelse](durable-functions-external-events.md), till exempel ett meddelande som genereras av en mänsklig interaktion.

Följande exempel skapar en godkännande process för att demonstrera de mänskliga interaktions mönstren:

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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

För att skapa en varaktig timer, `context.CreateTimer` anropa (.net) `context.df.createTimer` eller (Java Script). Meddelandet tas emot av `context.WaitForExternalEvent` (.net) eller `context.df.waitForExternalEvent` (Java Script). Sedan anropas `context.df.Task.any`(.net ) eller (Java Script) för att bestämma om de ska eskalera (tids gräns sker först) eller behandla godkännandet (godkännandet tas emot före tids gränsen). `Task.WhenAny`

En extern klient kan leverera händelse meddelandet till en väntande Orchestrator-funktion genom att antingen använda [inbyggda http-API: er](durable-functions-http-api.md#raise-event) eller genom att använda [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) -API: et från en annan funktion:

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

### <a name="aggregator"></a>Mönster #6: Aggregator (för hands version)

Det sjätte mönstret är att sammanställa händelse data under en viss tids period till en enda, adresser bar *entitet*. I det här mönstret kan de data som sammanställs komma från flera källor, levereras i batchar eller vara spridda över långa tids perioder. Aggregator kan behöva vidta åtgärder för händelse data när de anländer, och externa klienter kan behöva fråga de sammanställda data.

![Aggregator-diagram](./media/durable-functions-concepts/aggregator.png)

Det är svårt att försöka implementera det här mönstret med normala, tillstånds lösa funktioner är att samtidighets kontroll blir en enorm utmaning. Du behöver inte bara bekymra dig om flera trådar som ändrar samma data samtidigt. du måste också bekymra dig om att se till att Aggregator bara körs på en enda virtuell dator i taget.

Med en [hållbar enhets funktion](durable-functions-preview.md#entity-functions)kan en implementera det här mönstret enkelt som en enda funktion.

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

Varaktiga entiteter kan också modelleras som .NET-klasser. Detta kan vara användbart om listan över åtgärder blir stor och om den är statisk. Följande exempel är en motsvarande implementering av `Counter` entiteten med hjälp av .NET-klasser och-metoder.

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

Klienter kan köa *åtgärder* för (kallas även "signalering") en entitets funktion med hjälp `orchestrationClient` av bindningen.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Dynamiskt genererade proxyservrar är också tillgängliga för att signalera enheter på ett typ säkert sätt. Förutom att signalera kan klienter även fråga efter status för en entitets funktion med hjälp av metoder på `orchestrationClient` bindningen.

> [!NOTE]
> Enhets funktioner är för närvarande endast tillgängliga i [Durable Functions 2,0](durable-functions-preview.md)-förhands granskningen.

## <a name="the-technology"></a>Tekniken

Durable Functions-tillägget är byggt ovanpå det [varaktiga aktivitets ramverket](https://github.com/Azure/durabletask)i bakgrunden, ett bibliotek med öppen källkod på GitHub som används för att skapa varaktiga uppgifts dirigeringar. Precis som Azure Functions är Server lös utvecklingen av Azure WebJobs, Durable Functions är Server lös utvecklingen av det varaktiga aktivitets ramverket. Microsoft och andra organisationer använder det tåliga aktivitets ramverket i stor utsträckning för att automatisera verksamhets kritiska processer. Det är en naturlig anpassning för den serverbaserade Azure Functionss miljön.

### <a name="event-sourcing-checkpointing-and-replay"></a>Händelse källa, kontroll punkter och uppspelning

Orchestrator-funktioner upprätthåller sin körnings status på ett tillförlitligt sätt med hjälp av design mönstret för [händelse källor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . I stället för att direkt lagra det aktuella läget för ett dirigering, använder Durable Functions-tillägget en lagring med enbart tillägg för att registrera en fullständig serie åtgärder som funktions dirigeringen tar. En skrivskyddad lagrings plats har många fördelar jämfört med "dumpning", fullständig körnings status. Fördelarna är ökad prestanda, skalbarhet och svars tider. Du får också eventuell konsekvens för transaktions data och fullständig gransknings historik och historik. Gransknings historiken har stöd för pålitliga kompenserande åtgärder.

Durable Functions använder händelse källa transparent. I bakgrunden, `await` ger operatornC#() `yield` eller (Java Script) i en Orchestrator-funktion kontrollen av Orchestrator-tråden tillbaka till den varaktiga aktivitets Ramverks hanteraren. Dispatchern genomför sedan alla nya åtgärder som Orchestrator-funktionen schemalägger (till exempel anropa en eller flera underordnade funktioner eller schemalägga en varaktig timer) till lagringen. Åtgärden för att utföra transparent tillägg i körnings historiken för Orchestration-instansen. Historiken lagras i en lagrings tabell. Inchecknings åtgärden lägger sedan till meddelanden i en kö för att schemalägga det faktiska arbetet. I det här läget kan Orchestrator-funktionen tas bort från minnet. 

Faktureringen för Orchestrator-funktionen stannar om du använder Azure Functions förbruknings plan. När det finns mer arbete att utföra startar funktionen om och dess status rekonstrueras.

När en Orchestration-funktion får mer arbete (till exempel om ett svarsmeddelande tas emot eller om en varaktig timer upphör att gälla), aktiverar Orchestrator och kör om hela funktionen från början för att återskapa det lokala läget. 

Om koden försöker anropa en funktion (eller något annat asynkront arbete) under uppspelningen kan du se körnings historiken för den aktuella dirigeringen. Om den finner att [aktivitets funktionen](durable-functions-types-features-overview.md#activity-functions) redan har körts och ger ett resultat spelas den upp i resultatet och Orchestrator-koden fortsätter att köras. Repetitionen fortsätter tills funktions koden är avslutad eller tills den har schemalagt nytt asynkront arbete.

### <a name="orchestrator-code-constraints"></a>Begränsningar för Orchestrator-kod

Repetitions beteendet i Orchestrator Code skapar begränsningar för den typ av kod som du kan skriva i en Orchestrator-funktion. Till exempel måste Orchestrator-kod vara deterministisk eftersom den kommer att spelas upp flera gånger och det måste ge samma resultat varje gång. En fullständig lista över begränsningar finns i [Orchestrator Code-begränsningar](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

Durable Functions-tillägget avger automatiskt strukturerade spårnings data till [Application Insights](../functions-monitoring.md) om du konfigurerar din funktions app med en Azure Application Insights-Instrumentation-nyckel. Du kan använda spårnings data för att övervaka åtgärder och framsteg för dina dirigeringar.

Här är ett exempel på hur Durable Functions spårnings händelser ser ut på Application Insights Portal när du använder [Application Insights analys](../../application-insights/app-insights-analytics.md):

![Application Insights frågeresultat](./media/durable-functions-concepts/app-insights-1.png)

Du kan hitta användbara strukturerade data i `customDimensions` fältet i varje loggpost. Här är ett exempel på en post som är helt expanderad:

![Fältet customDimensions i en Application Insights fråga](./media/durable-functions-concepts/app-insights-2.png)

På grund av omuppspelnings beteendet för den varaktiga aktivitets Ramverks Dispatchern kan du se till att du ser redundanta logg poster för åtgärder som spelas upp. Redundanta logg poster kan hjälpa dig att förstå omuppspelnings beteendet för huvud motorn. I artikeln [diagnostik](durable-functions-diagnostics.md) visas exempel frågor som filtrerar bort uppspelnings loggar, så att du bara kan se "real tids"-loggar.

## <a name="storage-and-scalability"></a>Lagring och skalbarhet

Durable Functions-tillägget använder köer, tabeller och blobbar i Azure Storage för att spara tillstånd för körnings historik och utlösa funktions körning. Du kan använda standard lagrings kontot för Function-appen, eller så kan du konfigurera ett separat lagrings konto. Du kanske vill ha ett separat konto baserat på lagrings data flödes gränser. Orchestrator-koden du skriver fungerar inte med entiteterna i dessa lagrings konton. Det varaktiga aktivitets ramverket hanterar entiteterna direkt som en implementerings information.

Orchestrator Functions schemalägger aktivitets funktioner och tar emot sina svar via interna Kömeddelanden. När en Function-App körs i Azure Functions förbruknings planen övervakas dessa köer av [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) . Nya beräknings instanser läggs till vid behov. Vid utskalning till flera virtuella datorer kan en Orchestrator-funktion köras på en virtuell dator, medan aktivitets funktioner som fungerar som Orchestrator-funktions anrop kan köras på flera olika virtuella datorer. Mer information om skalnings beteendet för Durable Functions finns i [prestanda och skalning](durable-functions-perf-and-scale.md).

Körnings historiken för Orchestrator-konton lagras i Table Storage. När en instans rehydratiseras på en viss virtuell dator hämtar Orchestrator sin körnings historik från Table Storage så att den kan återskapa sitt lokala tillstånd. En praktisk aspekt av att ha historiken tillgänglig i Table Storage är att du kan använda verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) för att se historiken för dina dirigeringar.

Storage-blobbar används främst som en operationell leasing mekanism för att samordna utskalning av Orchestration-instanser över flera virtuella datorer. Storage-blobbar innehåller data för stora meddelanden som inte kan lagras direkt i tabeller eller köer.

![En skärm bild av Azure Storage Explorer](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Även om det är enkelt och bekvämt att se körnings historiken i Table Storage, ska du inte göra några beroenden i den här tabellen. Tabellen kan ändras när Durable Functions tillägget utvecklas.

## <a name="known-issues"></a>Kända problem

Alla kända problem bör spåras i listan med [GitHub-problem](https://github.com/Azure/azure-functions-durable-extension/issues) . Om du stöter på ett problem och inte kan hitta problemet i GitHub öppnar du ett nytt ärende. Innehåller en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

Mer information om Durable Functions finns i [Durable Functions funktions typer och-funktioner](durable-functions-types-features-overview.md). 

Så här kommer du igång:

> [!div class="nextstepaction"]
> [Skapa din första varaktiga funktion](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
