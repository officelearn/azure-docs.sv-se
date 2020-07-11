---
title: Hantera externa händelser i Durable Functions – Azure
description: Lär dig hur du hanterar externa händelser i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165557"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i Durable Functions (Azure Functions)

Orchestrator-funktioner kan vänta och lyssna efter externa händelser. Den här funktionen i [Durable Functions](durable-functions-overview.md) är ofta användbar för att hantera mänsklig interaktion eller andra externa utlösare.

> [!NOTE]
> Externa händelser är enkelriktade asynkrona åtgärder. De är inte lämpliga för situationer där klienten som skickar händelsen behöver ett synkront svar från Orchestrator-funktionen.

## <a name="wait-for-events"></a>Vänta på händelser

Metoderna [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.net) och `waitForExternalEvent` (Java Script) i [Dirigerings utlösaren](durable-functions-bindings.md#orchestration-trigger) tillåter att en Orchestrator-funktion asynkront väntar och lyssnar efter en extern händelse. Den lyssnande Orchestrator-funktionen deklarerar *namnet* på händelsen och *formen på de data* som det förväntar sig att ta emot.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Föregående C#-kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

---

Föregående exempel lyssnar efter en viss enskild händelse och vidtar åtgärder när den tas emot.

Du kan lyssna efter flera händelser samtidigt, som i följande exempel, vilket väntar på ett av tre möjliga händelse meddelanden.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Föregående C#-kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

---

Föregående exempel *lyssnar efter flera* händelser. Det är också möjligt att vänta på *alla* händelser.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

I .NET, om händelse nytto lasten inte kan konverteras till den förväntade typen `T` , genereras ett undantag.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

---

`WaitForExternalEvent`väntar på oändligt för vissa indatatyper.  Function-appen kan tas bort på ett säkert sätt vid väntan. Om och när en händelse tas emot för den här Orchestration-instansen, är den aktive ras automatiskt och bearbetar händelsen omedelbart.

> [!NOTE]
> Om din Function-app använder förbruknings planen uppstår inga fakturerings avgifter medan en Orchestrator-funktion väntar på en aktivitet från `WaitForExternalEvent` (.net) eller `waitForExternalEvent` (Java Script), oavsett hur lång tid det väntar.

## <a name="send-events"></a>Skicka händelser

Du kan använda metoderna [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) eller `raiseEventAsync` (Java Script) för att skicka en extern händelse till ett Orchestration. Dessa metoder exponeras av [Dirigerings klientens](durable-functions-bindings.md#orchestration-client) bindning. Du kan också använda den inbyggda funktionen för att skapa en extern händelse i [HTTP-API: et](durable-functions-http-api.md#raise-event) för att skicka en extern händelse till ett Orchestration.

En utlöst händelse innehåller ett *instans-ID*, en *EventName*och *eventData* som parametrar. Orchestrator Functions hanterar dessa händelser med `WaitForExternalEvent` API: erna (.net) eller `waitForExternalEvent` (Java Script). *EventName* måste matcha både sändnings-och mottagnings slut för att händelsen ska kunna bearbetas. Händelse data måste också vara JSON-serialiserbar.

Internt kallas "Utlös Event"-mekanismer ett meddelande som hämtas av den väntande Orchestrator-funktionen. Om instansen inte väntar på det angivna *händelse namnet* läggs händelse meddelandet till i en kö i minnet. Om Orchestration-instansen senare börjar lyssna efter detta *händelse namn,* kommer den att söka efter händelse meddelanden i kön.

> [!NOTE]
> Om det inte finns någon Dirigerings instans med angivet *instans-ID*, ignoreras händelse meddelandet.

Nedan visas ett exempel på en Queue-utlöst funktion som skickar en "godkännande"-händelse till en Orchestrator Function-instans. Dirigerings instansens ID kommer från bröd texten i Queue-meddelandet.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Föregående C#-kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `OrchestrationClient` attribut i stället för `DurableClient` attributet och du måste använda `DurableOrchestrationClient` parameter typen i stället för `IDurableOrchestrationClient` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Internt, `RaiseEventAsync` (.net) eller `raiseEvent` (Java Script), köas ett meddelande som hämtas av den väntande Orchestrator-funktionen. Om instansen inte väntar på det angivna *händelse namnet* läggs händelse meddelandet till i en kö i minnet. Om Orchestration-instansen senare börjar lyssna efter detta *händelse namn,* kommer den att söka efter händelse meddelanden i kön.

> [!NOTE]
> Om det inte finns någon Dirigerings instans med angivet *instans-ID*, ignoreras händelse meddelandet.

### <a name="http"></a>HTTP

Följande är ett exempel på en HTTP-begäran som genererar en "godkännande"-händelse till en Orchestration-instans. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

I det här fallet är instans-ID: t hårdkodad som *MyInstanceId*.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar fel hantering](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på mänsklig interaktion](durable-functions-phone-verification.md)