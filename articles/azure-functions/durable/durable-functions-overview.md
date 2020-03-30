---
title: Översikt över Durable Functions – Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241348"
---
# <a name="what-are-durable-functions"></a>Vad är Durable Functions?

*Varaktiga funktioner* är ett tillägg av [Azure Functions](../functions-overview.md) som låter dig skriva tillståndskänsliga funktioner i en serverlös beräkningsmiljö. Tillägget kan du definiera tillståndskänsliga arbetsflöden genom att skriva [*orchestrator-funktioner*](durable-functions-orchestrations.md) och tillståndskänsliga entiteter genom att skriva [*entitetsfunktioner*](durable-functions-entities.md) med hjälp av programmeringsmodellen Azure Functions. Bakom kulisserna hanterar tillägget tillstånd, kontrollpunkter och omstarter åt dig, så att du kan fokusera på din affärslogik.

## <a name="supported-languages"></a><a name="language-support"></a>Språk som stöds

Durable Functions stöder för närvarande följande språk:

* **C#**: både [förkompilerade klassbibliotek](../functions-dotnet-class-library.md) och [C#-skript](../functions-reference-csharp.md).
* **JavaScript**: stöds endast för version 2.x av Azure Functions-körningen. Kräver version 1.7.0 av Durable Functions-tillägget eller en senare version. 
* **F#**: både förkompilerade klassbibliotek och F#-skript. F#-skriptet stöds endast för version 1.x av Azure Functions-körningen.

Durable Functions har som mål att stödja alla [Azure Functions-språk](../supported-languages.md). I [Durable Functions-problemlistan](https://github.com/Azure/azure-functions-durable-extension/issues) finns senaste status för arbetet med att stödja ytterligare språk.

Precis som Azure Functions finns det mallar som hjälper dig att utveckla varaktiga funktioner med [Visual Studio 2019,](durable-functions-create-first-csharp.md) [Visual Studio Code](quickstart-js-vscode.md)och [Azure-portalen](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Programmönster

Det huvudsakliga användningsfallet för Durable Functions är att förenkla komplexa, tillståndskänsliga koordinationskrav i serverlösa program. I följande avsnitt beskrivs typiska programmönster som kan dra nytta av varaktiga funktioner:

* [Funktionslänkning](#chaining)
* [Förgrening](#fan-in-out)
* [Asynkrona HTTP-API:er](#async-http)
* [Övervakning](#monitoring)
* [Mänsklig interaktion](#human)
* [Aggregator (tillståndskänsliga enheter)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Mönster #1: Funktionskedja

I funktionskrändemönstret körs en sekvens av funktioner i en viss ordning. I det här mönstret används utdata för en funktion på indata från en annan funktion.

![Ett diagram över funktionskedjantätt](./media/durable-functions-concepts/function-chaining.png)

Du kan använda varaktiga funktioner för att implementera funktionskedjans mönster kortfattat enligt följande exempel.

I det här `F1`exemplet `F2` `F3`är `F4` värdena , , och namnen på andra funktioner i samma funktionsapp. Du kan implementera kontrollflöde med hjälp av normala kodningskonstruktioner. Koden körs uppifrån och ned. Koden kan omfatta befintliga språkkontrollflödessenmantik, till exempel villkor och loopar. Du kan inkludera felhanteringslogik `try` / `catch` / `finally` i block.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
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

Du kan `context` använda parametern för att anropa andra funktioner efter namn, skicka parametrar och returfunktionsutdata. Varje gång koden `await`anropar vägordnar ramverket Varaktiga Funktioner förloppet för den aktuella funktionsinstansen. Om processen eller den virtuella datorn återvinns halvvägs genom körningen `await` återupptas funktionsinstansen från föregående anrop. Mer information finns i nästa avsnitt, Mönster #2: Fläkta ut/fläkta in.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Du kan `context.df` använda objektet för att anropa andra funktioner efter namn, skicka parametrar och returfunktionsutdata. Varje gång koden `yield`anropar vägordnar ramverket Varaktiga Funktioner förloppet för den aktuella funktionsinstansen. Om processen eller den virtuella datorn återvinns halvvägs genom körningen `yield` återupptas funktionsinstansen från föregående anrop. Mer information finns i nästa avsnitt, Mönster #2: Fläkta ut/fläkta in.

> [!NOTE]
> Objektet `context` i JavaScript representerar hela [funktionskontexten](../functions-reference-node.md#context-object). Få tillgång till kontexten Varaktiga funktioner med egenskapen `df` i huvudkontexten.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Mönster #2: Fläkt ut / fläkt i

I fläkten ut / fläkt i mönster, du kör flera funktioner parallellt och sedan vänta på att alla funktioner ska avslutas. Ofta utförs en del aggregeringsarbete på de resultat som returneras från funktionerna.

![Ett diagram över fläkten ut / fläkt mönster](./media/durable-functions-concepts/fan-out-fan-in.png)

Med normala funktioner kan du fläkta ut genom att funktionen skickar flera meddelanden till en kö. Fanning tillbaka i är mycket mer utmanande. För att fläkta in, i en normal funktion, skriver du kod för att spåra när kö-utlöst funktioner, och sedan lagra funktion utdata.

Tillägget Durable Functions hanterar detta mönster med relativt enkel kod:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
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

Fläkt-ut arbetet distribueras till flera `F2` instanser av funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `Task.WhenAll`uppmanas att vänta på att alla de anropade funktionerna ska slutföras. Sedan sammanställs `F2` funktionsutgångarna från den dynamiska uppgiftslistan och skickas till `F3` funktionen.

Den automatiska kontrollpunkt `await` som `Task.WhenAll` sker vid anropet på säkerställer att en potentiell krasch eller omstart inte kräver att en redan slutförd uppgift startas om.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Fläkt-ut arbetet distribueras till flera `F2` instanser av funktionen. Arbetet spåras med hjälp av en dynamisk lista med aktiviteter. `context.df.Task.all`API anropas för att vänta på att alla anropade funktioner ska slutföras. Sedan sammanställs `F2` funktionsutgångarna från den dynamiska uppgiftslistan och skickas till `F3` funktionen.

Den automatiska kontrollpunkt `yield` som `context.df.Task.all` sker vid anropet på säkerställer att en potentiell krasch eller omstart inte kräver att en redan slutförd uppgift startas om.

---

> [!NOTE]
> I sällsynta fall är det möjligt att en krasch kan inträffa i fönstret efter att en aktivitetsfunktion har slutförts men innan den har slutförts sparas i orchestration-historiken. Om detta inträffar körs aktivitetsfunktionen igen från början efter att processen har återställts.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Mönster #3: Async HTTP-API:er

Async HTTP API-mönstret åtgärdar problemet med att samordna tillståndet för långvariga åtgärder med externa klienter. Ett vanligt sätt att implementera det här mönstret är att låta en HTTP-slutpunkt utlösa den tidskrävande åtgärden. Omdirigera sedan klienten till en statusslutpunkt som klienten avbiris för att lära sig när åtgärden är klar.

![Ett diagram över HTTP API-mönstret](./media/durable-functions-concepts/async-http-api.png)

Varaktiga funktioner ger **inbyggt stöd** för det här mönstret, vilket förenklar eller till och med tar bort den kod du behöver skriva för att interagera med tidskrävande funktionskörningar. Snabbstartsexempel för varaktiga funktioner ([C#](durable-functions-create-first-csharp.md) och [JavaScript](quickstart-js-vscode.md)) visar till exempel ett enkelt REST-kommando som du kan använda för att starta nya orchestrator-funktionsinstanser. När en instans har startats exponeras webhook-HTTP-API:er som frågar orchestrator-funktionens status. 

I följande exempel visas REST-kommandon som startar en orchestrator och frågar dess status. För tydlighetens skull utelämnas vissa protokollinformation från exemplet.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Eftersom körningen varaktiga funktioner hanterar tillståndet åt dig behöver du inte implementera din egen statusspårningsmekanism.

Tillägget Varaktiga funktioner exponerar inbyggda HTTP-API:er som hanterar långvariga orkestreringar. Du kan alternativt implementera det här mönstret själv genom att använda egna funktionsutlösare (till exempel HTTP, en kö eller Azure Event Hubs) och [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client). Du kan till exempel använda ett kömeddelande för att utlösa uppsägning. Du kan också använda en HTTP-utlösare som skyddas av en Azure Active Directory-autentiseringsprincip i stället för de inbyggda HTTP-API:er som använder en genererad nyckel för autentisering.

Mer information finns i [http-funktionsartikeln,](durable-functions-http-features.md) där du förklarar hur du kan exponera asynkrona, tidskrävande processer via HTTP med tillägget Varaktiga funktioner.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Mönster #4: Bildskärm

Monitormönstret refererar till en flexibel, återkommande process i ett arbetsflöde. Ett exempel är avsökning tills specifika villkor är uppfyllda. Du kan använda en vanlig [timerutlösare](../functions-bindings-timer.md) för att hantera ett grundläggande scenario, till exempel ett periodiskt rensningsjobb, men intervallet är statiskt och hanteringen av instanslivstiderna blir komplex. Du kan använda varaktiga funktioner för att skapa flexibla upprepningsintervall, hantera aktivitetens livstider och skapa flera övervakningsprocesser från en enda orkestrering.

Ett exempel på övervakningsmönstret är att vända det tidigare async HTTP API-scenariot. I stället för att exponera en slutpunkt för en extern klient för att övervaka en tidskrävande åtgärd förbrukar den långvariga övervakaren en extern slutpunkt och väntar sedan på en tillståndsändring.

![Ett diagram över monitormönstret](./media/durable-functions-concepts/monitor.png)

På några få kodrader kan du använda varaktiga funktioner för att skapa flera bildskärmar som observerar godtyckliga slutpunkter. Övervakarna kan avsluta körningen när ett villkor uppfylls, eller så kan en annan funktion använda den varaktiga orchestration-klienten för att avsluta övervakarna. Du kan ändra en `wait` bildskärms intervall baserat på ett visst villkor (till exempel exponentiell backoff.) 

Följande kod implementerar en grundläggande övervakare:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
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

---

När en begäran tas emot skapas en ny orchestration-instans för det jobb-ID:t. Instansen avböd en status tills ett villkor uppfylls och loopen avslutas. En varaktig timer styr avsökningsintervallet. Sedan kan mer arbete utföras eller orkestreringen avslutas. När `nextCheck` den `expiryTime`är över slutar bildskärmen.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Mönster #5: Mänsklig interaktion

Många automatiserade processer innebär någon form av mänsklig interaktion. Att involvera människor i en automatiserad process är svårt eftersom människor inte är lika högtillgängliga och lika lyhörda som molntjänster. En automatiserad process kan möjliggöra den här interaktionen med hjälp av timeout och kompensationslogik.

En godkännandeprocess är ett exempel på en affärsprocess som involverar mänsklig interaktion. Godkännande från en chef kan krävas för en utgiftsrapport som överstiger ett visst belopp i dollar. Om chefen inte godkänner utgiftsrapporten inom 72 timmar (kanske chefen åkte på semester), en eskalering process sparkar i för att få godkännande från någon annan (kanske chefens chef).

![Ett diagram över det mänskliga interaktionsmönstret](./media/durable-functions-concepts/approval.png)

Du kan implementera mönstret i det här exemplet med hjälp av en orchestrator-funktion. Orchestrator använder en [varaktig timer](durable-functions-timers.md) för att begära godkännande. Orchestrator eskalerar om timeout inträffar. Orchestrator väntar på en [extern händelse](durable-functions-external-events.md), till exempel ett meddelande som genereras av en mänsklig interaktion.

Dessa exempel skapar en godkännandeprocess för att demonstrera det mänskliga interaktionsmönstret:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
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
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Om du vill skapa `context.CreateTimer`den hållbara timern anropar du . Anmälan tas emot `context.WaitForExternalEvent`av . Sedan `Task.WhenAny` anropas för att bestämma om du vill eskalera (timeout sker först) eller bearbeta godkännandet (godkännandet tas emot före timeout).

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Om du vill skapa `context.df.createTimer`den hållbara timern anropar du . Anmälan tas emot `context.df.waitForExternalEvent`av . Sedan `context.df.Task.any` anropas för att bestämma om du vill eskalera (timeout sker först) eller bearbeta godkännandet (godkännandet tas emot före timeout).

---

En extern klient kan leverera händelsemeddelandet till en väntande orchestrator-funktion med hjälp av de [inbyggda HTTP-API:erna:](durable-functions-http-api.md#raise-event)

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

En händelse kan också höjas med den varaktiga orchestration-klienten från en annan funktion i samma funktionsapp:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Mönster #6: Aggregator (tillståndskänsliga entiteter)

Det sjätte mönstret handlar om att samla händelsedata över en tidsperiod till en enda, adresserbar *entitet*. I det här mönstret kan de data som aggregeras komma från flera källor, levereras i batchar eller spridas över långa tidsperioder. Aggregatorn kan behöva vidta åtgärder för händelsedata när den anländer, och externa klienter kan behöva fråga de aggregerade data.

![Aggregatordiagram](./media/durable-functions-concepts/aggregator.png)

Det knepiga med att försöka genomföra detta mönster med normala, tillståndslösa funktioner är att samtidighet kontroll blir en enorm utmaning. Du behöver inte bara oroa dig för flera trådar som ändrar samma data samtidigt, du måste också oroa dig för att se till att aggregatorn bara körs på en enda virtuell dator i taget.

Du kan använda [varaktiga entiteter](durable-functions-entities.md) för att enkelt implementera det här mönstret som en enda funktion.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Varaktiga entiteter kan också modelleras som klasser i .NET. Den här modellen kan vara användbar om listan över åtgärder är fast och blir stor. Följande exempel är en motsvarande `Counter` implementering av entiteten med hjälp av .NET-klasser och metoder.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Klienter kan köa *operationer* för (kallas även "signalering") en entitetsfunktion med hjälp av [entitetsklientbindningen](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamiskt genererade proxyservrar finns också i .NET för signalentiteter på ett typsäkert sätt. Och förutom signalering kan klienter också fråga efter tillståndet för en entitetsfunktion med hjälp av [typsäkra metoder](durable-functions-bindings.md#entity-client-usage) på orchestration-klientbindningen.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Entitetsfunktioner är tillgängliga i [Varaktiga funktioner 2.0](durable-functions-versions.md) och högre.

## <a name="the-technology"></a>Tekniken

Bakom kulisserna bygger tillägget Varaktiga funktioner ovanpå [Durable Task Framework](https://github.com/Azure/durabletask), ett bibliotek med öppen källkod på GitHub som används för att skapa arbetsflöden i kod. Liksom Azure Functions är den serverlösa utvecklingen av Azure WebJobs är varaktiga funktioner den serverlösa utvecklingen av durable task framework. Microsoft och andra organisationer använder durable task framework i stor utsträckning för att automatisera verksamhetskritiska processer. Det är en naturlig passform för den serverlösa Azure Functions-miljön.

## <a name="code-constraints"></a>Kodbegränsningar

För att ge tillförlitliga och långvariga körningsgarantier har orchestrator-funktioner en uppsättning kodningsregler som måste följas. Mer information finns i artikeln [Orchestrator-funktionskodbegränsningar.](durable-functions-code-constraints.md)

## <a name="billing"></a>Fakturering

Durable Functions debiteras samma som Azure Functions. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/). När orchestrator-funktioner körs i Azure Functions [Consumption-planen](../functions-scale.md#consumption-plan)finns det vissa faktureringsbeteenden att vara medveten om. Mer information om dessa beteenden finns i faktureringsartikeln [för varaktiga funktioner.](durable-functions-billing.md)

## <a name="jump-right-in"></a>Kom igång snabbt

Du kan komma igång med Durable Functions på mindre än 10 minuter genom att slutföra någon av dessa språkspecifika snabbstartsguider:

* [C# med Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript med hjälp av Visual Studio Code](quickstart-js-vscode.md)

I båda snabbstarterna skapar du och testar en beständig ”hello world”-funktion lokalt. Du publicerar sedan funktionskoden till Azure. Den funktion som du skapar orkestrerar och kedjar samman anrop till andra funktioner.

## <a name="learn-more"></a>Läs mer

Följande video visar fördelarna med Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

En mer djupgående diskussion om varaktiga funktioner och den underliggande tekniken finns i följande video (den är fokuserad på .NET, men begreppen gäller även för andra språk som stöds):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Eftersom Durable Functions är ett avancerat tillägg för [Azure Functions](../functions-overview.md) är det inte lämpligt för alla program. En jämförelse med andra Azure-orkestreringstekniker finns i avsnittet om att [jämföra Azure Functions med Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Funktionstyper och funktioner för varaktiga funktioner](durable-functions-types-features-overview.md)
