---
title: Hantera externa händelser i Durable Functions – Azure
description: Lär dig hur du hanterar externa händelser i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1c2a2f08c31c427241bbd5e482906118a90ee178
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614839"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i Durable Functions (Azure Functions)

Orchestrator-funktioner kan vänta och lyssna efter externa händelser. Den här funktionen i [Durable Functions](durable-functions-overview.md) är ofta användbar för att hantera mänsklig interaktion eller andra externa utlösare.

> [!NOTE]
> Externa händelser är enkelriktade asynkrona åtgärder. De är inte lämpliga för situationer där klienten som skickar händelsen behöver ett synkront svar från Orchestrator-funktionen.

## <a name="wait-for-events"></a>Vänta på händelser

Metoderna `WaitForExternalEvent` (.NET) och `waitForExternalEvent` (Java Script) i [bindnings utlösaren](durable-functions-bindings.md#orchestration-trigger) gör att en Orchestrator-funktion asynkront kan vänta och lyssna efter en extern händelse. Den lyssnande Orchestrator-funktionen deklarerar *namnet* på händelsen och *formen på de data* som det förväntar sig att ta emot.

### <a name="c"></a>C#

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
> Föregående C# kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

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

Föregående exempel lyssnar efter en viss enskild händelse och vidtar åtgärder när den tas emot.

Du kan lyssna efter flera händelser samtidigt, som i följande exempel, vilket väntar på ett av tre möjliga händelse meddelanden.

#### <a name="c"></a>C#

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
> Föregående C# kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

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

Föregående exempel *lyssnar efter flera* händelser. Det är också möjligt att vänta på *alla* händelser.

#### <a name="c"></a>C#

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
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

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

`WaitForExternalEvent` väntar oändligt för vissa ingångar.  Function-appen kan tas bort på ett säkert sätt vid väntan. Om och när en händelse tas emot för den här Orchestration-instansen, är den aktive ras automatiskt och bearbetar händelsen omedelbart.

> [!NOTE]
> Om din Function-app använder förbruknings planen uppstår inga fakturerings avgifter medan en Orchestrator-funktion väntar på en aktivitet från `WaitForExternalEvent` (.NET) eller `waitForExternalEvent` (Java Script), oavsett hur lång tid det väntar.

I .NET, om händelse nytto lasten inte kan konverteras till den förväntade typen `T`, genereras ett undantag.

## <a name="send-events"></a>Skicka händelser

Metoden `RaiseEventAsync` (.NET) eller `raiseEvent` (Java Script) i [Dirigerings klient bindningen](durable-functions-bindings.md#orchestration-client) skickar händelser som `WaitForExternalEvent` (.net) eller `waitForExternalEvent` (Java Script) väntar på.  Metoden `RaiseEventAsync` använder sig av parametrarna *EventName* och *eventData* som parametrar. Händelse data måste vara JSON-serialiserbar.

Nedan visas ett exempel på en Queue-utlöst funktion som skickar en "godkännande"-händelse till en Orchestrator Function-instans. Dirigerings instansens ID kommer från bröd texten i Queue-meddelandet.

### <a name="c"></a>C#

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
> Föregående C# kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `OrchestrationClient` attribut i stället för attributet `DurableClient` och du måste använda `DurableOrchestrationClient` parameter typ i stället för `IDurableOrchestrationClient`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internt, `RaiseEventAsync` (.NET) eller `raiseEvent` (Java Script) i kö ett meddelande som hämtas av den väntande Orchestrator-funktionen. Om instansen inte väntar på det angivna *händelse namnet* läggs händelse meddelandet till i en kö i minnet. Om Orchestration-instansen senare börjar lyssna efter detta *händelse namn,* kommer den att söka efter händelse meddelanden i kön.

> [!NOTE]
> Om det inte finns någon Dirigerings instans med angivet *instans-ID*, ignoreras händelse meddelandet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar fel hantering](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på mänsklig interaktion](durable-functions-phone-verification.md)