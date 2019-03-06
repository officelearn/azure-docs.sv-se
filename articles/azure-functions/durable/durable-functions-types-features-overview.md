---
title: Funktionstyper och funktioner i tillägget varaktiga funktioner i Azure Functions
description: Läs mer om vilka typer av funktioner och roller som har stöd för funktionen till funktionen kommunikation i en varaktiga funktioner orchestration i Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455742"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Varaktiga funktioner typer och funktioner (Azure Functions)

Varaktiga funktioner är en utökning av [Azure Functions](../functions-overview.md). Du kan använda varaktiga funktioner för tillståndskänslig orkestrering av körning av funktion. En hållbar funktion är en lösning som består av olika Azure functions. Functions kan spela upp olika roller i en hållbar funktionen dirigering. 

Den här artikeln ger en översikt över typerna av funktioner som kan användas i en varaktiga funktioner dirigering. Artikeln innehåller vissa vanliga mönster som du kan använda för att ansluta functions. Lär dig hur varaktiga funktioner kan hjälpa dig att lösa dina utmaningar för app-utveckling.

![En bild som visar typerna av varaktiga funktioner][1]  

## <a name="types-of-durable-functions"></a>Typer av varaktiga funktioner

Du kan använda tre funktionstyper av varaktiga i Azure Functions: inloggningsaktivitet, orchestrator och klienten.

### <a name="activity-functions"></a>Aktivitetsfunktioner

Aktivitetsfunktioner är den grundläggande enheten för arbete i en hållbar funktionen dirigering. Aktivitetsfunktioner är de funktioner och uppgifter som orkestreras i processen. Du kan till exempel skapa en hållbar funktion för att bearbeta en order. Uppgifterna omfattar kontrollerar inventeringen debitera kunden och skapar en försändelse. Varje aktivitet är en funktion för aktiviteten. 

Aktivitetsfunktioner är inte begränsad i vilken typ av arbete som du kan göra i dem. Du kan skriva en funktion för aktiviteten i någon [språk som har stöd för varaktiga funktioner](durable-functions-overview.md#language-support). Hållbar uppgift framework garanterar att varje funktion kallad aktivitet körs minst en gång under en orkestrering.

Använd en [aktivitet utlösaren](durable-functions-bindings.md#activity-triggers) att utlösa en aktivitet-funktion. .NET-funktioner får en [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som en parameter. Du kan också binda utlösaren till alla andra objekt att skicka in indata till funktionen. I JavaScript, kan du komma åt indata via den `<activity trigger binding name>` egenskap på den [ `context.bindings` objektet](../functions-reference-node.md#bindings).

Funktionen aktivitet kan också returnera värden till orchestrator. Om du skickar eller returnera ett stort antal värden från en aktivitet-funktion, kan du använda [tupplar eller matriser](durable-functions-bindings.md#passing-multiple-parameters). Du kan utlösa en aktivitet funktion från en orchestration-instans. Även om en aktivitet-funktion och en annan funktion (till exempel en HTTP-utlöst funktion) kan dela kod, kan varje funktion har bara en utlösare.

Mer information och exempel finns i [Aktivitetsfunktioner](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Orchestrator-funktioner

Orchestrator-funktioner beskrivs hur åtgärder utförs och ordningen i vilken åtgärder utförs. Orchestrator-funktioner beskrivs orchestration i kod (C# eller JavaScript) enligt [varaktiga funktioner, mönster och tekniska begrepp](durable-functions-concepts.md). En orkestrering kan ha många olika typer av åtgärder, inklusive [Aktivitetsfunktioner](#activity-functions), [underordnade orkestreringar](#sub-orchestrations), [väntar på externa händelser](#external-events), och [timers](#durable-timers). 

En orchestrator-funktion måste aktiveras via en [orchestration utlösaren](durable-functions-bindings.md#orchestration-triggers).

En initierare har startats med en [orchestrator klienten](#client-functions). Du kan utlösa orchestrator från valfri källa (http-, kö, händelseströmmen). Varje instans av en orkestrering har en instansidentifierare. Instans-ID kan vara automatiskt genererade (rekommenderas) eller användare. Du kan använda instans-ID som [hantera instanser](durable-functions-instance-management.md) för dirigering.

Mer information och exempel finns i [Orchestration utlösare](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Klientfunktioner

Klientfunktioner är de utlösta funktioner som skapar nya instanser av en orkestrering. Klientfunktioner är startpunkten för att skapa en instans av en varaktiga funktioner dirigering. Du kan utlösa en klient-funktion från valfri källa (http-, kö, händelseströmmen). Du kan skriva en klient-funktion på valfritt språk som stöds av appen. 

Klientfunktioner har också en [orkestreringsklient](durable-functions-bindings.md#orchestration-client) bindning. En funktion som klienten kan använda orkestreringsklient bindning för att skapa och hantera varaktiga orkestreringar. 

Det mest grundläggande exemplet på en klient-funktion är en HTTP-utlöst funktion som startar en orchestrator-funktion och returnerar svaret Kontrollera status. Ett exempel finns i [HTTP-URL för API-identifiering](durable-functions-http-api.md#http-api-url-discovery).

Mer information och exempel finns i [orkestreringsklient](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funktioner och mönster

I nästa avsnitt beskrivs funktioner och mönster för varaktiga funktioner typer.

### <a name="sub-orchestrations"></a>Sub-orkestreringar

Orchestrator-funktioner kan anropa Aktivitetsfunktioner, men de kan också anropa andra orchestrator-funktioner. Du kan till exempel skapa en större dirigering av ett bibliotek av orchestrator-funktioner. Eller du kan köra flera instanser av en orchestrator-funktion parallellt.

Mer information och exempel finns i [underordnade orkestreringar](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Hållbar timers

[Varaktiga funktioner](durable-functions-overview.md) ger *varaktiga timers* som du kan använda i orchestrator-funktioner att implementera fördröjningar eller ställa in timeouter på asynkrona åtgärder. Använda beständiga timers i orchestrator-funktioner i stället för `Thread.Sleep` och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (JavaScript).

Mer information och exempel finns i [varaktiga timers](durable-functions-timers.md).

### <a name="external-events"></a>Externa händelser

Orchestrator-funktioner kan vänta på externa händelser att uppdatera en orchestration-instans. Det här varaktiga funktioner-funktionen är ofta användbara för att hantera en mänsklig interaktion eller andra externa återanrop.

Mer information och exempel finns i [externa händelser](durable-functions-external-events.md).

### <a name="error-handling"></a>Felhantering

Använd kod för att implementera varaktiga funktioner orkestreringar. Du kan använda funktioner för felhantering i programmeringsspråket. Mönster som `try` / `catch` fungerar som din orkestrering. 

Varaktiga funktioner levereras också med inbyggda återförsöksprinciper. En åtgärd kan fördröja och försök aktiviteter automatiskt när ett undantag inträffar. Du kan använda återförsök för att hantera tillfälliga undantag utan att överge dirigering.

Mer information och exempel finns i [felhantering](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Kommunikation mellan funktionsapp

Även om en hållbar orchestration körs i kontexten för en enda funktionsapp, kan du använda mönster för att koordinera orkestreringar mellan många funktionsappar. Kommunikation mellan appar som kan uppstå via HTTP, men med hjälp av beständiga ramverket för varje aktivitet innebär att du kan fortfarande ha en beständig process i två appar.

Följande exempel visar flera funktionsapp orchestration i C# och JavaScript. I varje exempel startar en aktivitet för externa samordning. En annan aktivitet hämtar och returnerar status. Orchestrator ska vänta tills statusen vara `Complete` innan du fortsätter.

Här följer några exempel på flera funktionsapp orchestration:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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

Kom igång genom att skapa din första varaktiga funktion i [ C# ](durable-functions-create-first-csharp.md) eller [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Läs mer om varaktiga funktioner](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
