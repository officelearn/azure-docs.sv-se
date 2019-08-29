---
title: Hantera externa händelser i Durable Functions – Azure
description: Lär dig hur du hanterar externa händelser i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9c546064589e82cfef367978ebea98c2c202307
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087293"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i Durable Functions (Azure Functions)

Orchestrator-funktioner kan vänta och lyssna efter externa händelser. Den här funktionen i [Durable Functions](durable-functions-overview.md) är ofta användbar för att hantera mänsklig interaktion eller andra externa utlösare.

> [!NOTE]
> Externa händelser är enkelriktade asynkrona åtgärder. De är inte lämpliga för situationer där klienten som skickar händelsen behöver ett synkront svar från Orchestrator-funktionen.

## <a name="wait-for-events"></a>Vänta på händelser

Metoden [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) låter en Orchestrator-funktion asynkront vänta och lyssna efter en extern händelse. Den lyssnande Orchestrator-funktionen deklarerar *namnet* på händelsen och *formen på de data* som det förväntar sig att ta emot.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

Föregående exempel lyssnar efter flera händelser . Det är också möjligt att vänta på *alla* händelser.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) väntar oändligt för vissa ingångar.  Function-appen kan tas bort på ett säkert sätt vid väntan. Om och när en händelse tas emot för den här Orchestration-instansen, är den aktive ras automatiskt och bearbetar händelsen omedelbart.

> [!NOTE]
> Om din Function-app använder förbruknings planen uppstår inga fakturerings avgifter medan en Orchestrator-funktion väntar på en aktivitet från `WaitForExternalEvent` (.net) eller `waitForExternalEvent` (Java Script), oavsett hur lång tid det väntar.

I .net, om händelse nytto lasten inte kan konverteras till den `T`förväntade typen, genereras ett undantag.

## <a name="send-events"></a>Skicka händelser

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen skickar händelserna som `WaitForExternalEvent` (.net) eller `waitForExternalEvent` (Java Script) väntar på.  Metoden tar EventName och *eventData* som parametrar. `RaiseEventAsync` Händelse data måste vara JSON-serialiserbar.

Nedan visas ett exempel på en Queue-utlöst funktion som skickar en "godkännande"-händelse till en Orchestrator Function-instans. Dirigerings instansens ID kommer från bröd texten i Queue-meddelandet.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internt, `RaiseEventAsync` (.net) eller `raiseEvent` (Java Script), köas ett meddelande som hämtas av den väntande Orchestrator-funktionen. Om instansen inte väntar på det angivna *händelse namnet* läggs händelse meddelandet till i en kö i minnet. Om Orchestration-instansen senare börjar lyssna efter detta *händelse namn,* kommer den att söka efter händelse meddelanden i kön.

> [!NOTE]
> Om det inte finns någon Dirigerings instans med angivet *instans-ID*, ignoreras händelse meddelandet. Mer information om det här problemet finns i [GitHub-problemet](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> När du utvecklar lokalt i Java Script måste du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t. ex. `localhost:7071`använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i [GitHub-problemet](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar Eternal-dirigeringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Köra ett exempel som väntar på externa händelser](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på mänsklig interaktion](durable-functions-phone-verification.md)