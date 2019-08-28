---
title: Funktions typer och funktioner i Durable Functions tillägget för Azure Functions
description: Lär dig mer om de typer av funktioner och roller som stöder funktions-till-funktion-kommunikation i en Durable Functions dirigering i Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097743"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions typer och funktioner (Azure Functions)

Durable Functions är en utökning av [Azure Functions](../functions-overview.md). Du kan använda Durable Functions för tillstånds känslig dirigering av funktions körning. En varaktig funktion är en lösning som består av olika Azure Functions. Funktioner kan spela olika roller i en varaktig funktions dirigering. 

Den här artikeln ger en översikt över vilka typer av funktioner som du kan använda i ett Durable Functions Orchestration. Artikeln innehåller några vanliga mönster som du kan använda för att ansluta funktioner. Lär dig hur Durable Functions kan hjälpa dig att lösa dina utmaningar med att utveckla appar.

![En bild som visar typerna av varaktiga funktioner][1]  

## <a name="types-of-durable-functions"></a>Typer av varaktiga funktioner

Du kan använda fyra varaktiga funktions typer i Azure Functions: aktivitet, Orchestrator, entitet och klient.

### <a name="activity-functions"></a>Aktivitets funktioner

Aktivitets funktionerna är den grundläggande arbets enheten i en varaktig funktions dirigering. Aktivitets funktioner är de funktioner och uppgifter som samordnas i processen. Du kan till exempel skapa en varaktig funktion för bearbetning av en order. Uppgifterna omfattar att kontrol lera inventeringen, debitera kunden och skapa en försändelse. Varje aktivitet är en aktivitets funktion. 

Aktivitets funktioner är inte begränsade i den typ av arbete du kan göra i dem. Du kan skriva en aktivitets funktion på alla [språk som Durable Functions stöd](durable-functions-overview.md#language-support)för. Det varaktiga aktivitets ramverket garanterar att varje anropad aktivitets funktion utförs minst en gång under ett dirigering.

Använd en [aktivitets](durable-functions-bindings.md#activity-triggers) utlösare för att utlösa en aktivitets funktion. .NET Functions tar emot en [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som en parameter. Du kan också binda utlösaren till ett annat objekt för att överföra indata till funktionen. I Java Script kan du komma åt inmatade `<activity trigger binding name>` [ `context.bindings` objekt](../functions-reference-node.md#bindings)via egenskapen på objektet.

Din aktivitets funktion kan också returnera värden till Orchestrator. Om du skickar eller returnerar ett stort antal värden från en aktivitets funktion kan du använda tupler [eller matriser](durable-functions-bindings.md#passing-multiple-parameters). Du kan endast utlösa en aktivitets funktion från en Orchestration-instans. Även om en aktivitets funktion och en annan funktion (t. ex. en HTTP-utlöst funktion) kan dela kod, kan varje funktion bara ha en utlösare.

Mer information och exempel finns i [aktivitets funktioner](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Orchestrator-funktioner

Orchestrator Functions beskriver hur åtgärder utförs och i vilken ordning åtgärder utförs. Orchestrator Functions beskriver dirigeringen i kod (C# eller Java Script) som visas i [Durable Functions mönster och tekniska begrepp](durable-functions-concepts.md). En dirigering kan ha många olika typer av åtgärder, inklusive [aktivitets funktioner](#activity-functions), [under dirigering](#sub-orchestrations), [som väntar på externa händelser](#external-events)och timers. [](#durable-timers) Orchestrator-funktioner kan också samverka med [enhets funktioner](#entity-functions).

En Orchestrator-funktion måste utlösas av en [Orchestration](durable-functions-bindings.md#orchestration-triggers)-utlösare.

En Orchestrator startas av en [Orchestrator-klient](#client-functions). Du kan utlösa Orchestrator från vilken källa som helst (HTTP, kö, händelse ström). Varje instans av en dirigering har en instans identifierare. Instans identifieraren kan skapas automatiskt (rekommenderas) eller genereras av användaren. Du kan använda instans identifieraren för att [Hantera instanser](durable-functions-instance-management.md) av dirigeringen.

Mer information och exempel finns i [Orchestration](durable-functions-bindings.md#orchestration-triggers)-utlösare.

###  <a name="entity-functions"></a>Enhets funktioner (förhands granskning)

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd,så kallade varaktiga entiteter. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskildutlösnings typ, enhets utlösare. Till skillnad från Orchestrator-funktioner har enhets funktioner inga speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

> [!NOTE]
> Enhets funktioner och relaterade funktioner är bara tillgängliga i Durable Functions 2,0 och senare.

Mer information om entitets funktioner finns i dokumentationen [](durable-functions-preview.md#entity-functions) för funktionen för förhands granskning av entiteter.

### <a name="client-functions"></a>Klient funktioner

Klient funktioner utlöser funktioner som skapar och hanterar instanser av dirigering och entiteter. De är i praktiken start punkten för att interagera med Durable Functions. Du kan utlösa en klient funktion från vilken källa som helst (HTTP, kö, händelse ström osv.). En klient funktion använder [Dirigerings klientens bindning](durable-functions-bindings.md#orchestration-client) för att skapa och hantera beständiga dirigeringar och entiteter.

Det mest grundläggande exemplet på en klient funktion är en HTTP-utlöst funktion som startar en Orchestrator-funktion och som sedan returnerar ett kontroll status svar. Ett exempel finns i [http API URL Discovery](durable-functions-http-api.md#http-api-url-discovery).

Mer information och exempel finns i [Orchestration client](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funktioner och mönster

I nästa avsnitt beskrivs funktionerna och mönstren i Durable Functions typer.

### <a name="sub-orchestrations"></a>Sub-orkestreringar

Orchestrator-funktioner kan anropa aktivitets funktioner, men de kan också anropa andra Orchestrator-funktioner. Du kan till exempel bygga ett större dirigering från ett bibliotek med Orchestrator-funktioner. Du kan också köra flera instanser av en Orchestrator-funktion parallellt.

Mer information och exempel finns i [under dirigering](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Varaktiga timers

[Durable Functions](durable-functions-overview.md) tillhandahåller *varaktiga timers* som du kan använda i Orchestrator-funktioner för att implementera fördröjningar eller ställa in tids gränser för asynkrona åtgärder. Använd varaktiga timers i Orchestrator-funktioner `Thread.Sleep` i `Task.Delay` ställetC#för och `setTimeout()` ( `setInterval()` ) eller och (Java Script).

Mer information och exempel finns i varaktiga [timers](durable-functions-timers.md).

### <a name="external-events"></a>Externa händelser

Orchestrator-funktioner kan vänta på att externa händelser uppdaterar en Orchestration-instans. Den här Durable Functions funktionen är ofta användbar för att hantera en mänsklig interaktion eller andra externa återanrop.

Mer information och exempel finns i [externa händelser](durable-functions-external-events.md).

### <a name="error-handling"></a>Felhantering

Använd kod för att implementera Durable Functions-dirigeringar. Du kan använda fel hanterings funktionerna i programmeringsspråket. Mönster som `try` / fungeraridittdirigering`catch` . 

Durable Functions även med inbyggda principer för återförsök. En åtgärd kan fördröja och försöka utföra aktiviteter automatiskt när ett undantag inträffar. Du kan använda återförsök för att hantera tillfälliga undantag utan att överge dirigeringen.

Mer information och exempel finns i [fel hantering](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Kommunikation mellan funktioner

Även om en varaktig dirigering körs i en enda Function-app, kan du använda mönster för att koordinera dirigeringar över många Function-appar. Kommunikation mellan appar kan ske över HTTP, men med hjälp av det varaktiga ramverket för varje aktivitet kan du fortfarande underhålla en varaktig process över två appar.

I följande exempel demonstreras samverkan mellan appar i C# och Java Script. I varje exempel startar en aktivitet den externa dirigeringen. En annan aktivitet hämtar och returnerar statusen. Orchestrator väntar på att statusen ska vara `Complete` innan den fortsätter.

Här följer några exempel på program dirigering mellan olika funktioner:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Nästa steg

Kom igång genom att skapa din första varaktiga funktion [C#](durable-functions-create-first-csharp.md) i eller [Java Script](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Läs mer om Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
