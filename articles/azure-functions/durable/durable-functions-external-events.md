---
title: Hantering av externa händelser i varaktiga funktioner - Azure
description: Lär dig hur du hanterar externa händelser i tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262970"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantering av externa händelser i varaktiga funktioner (Azure-funktioner)

Orchestrator-funktioner har möjlighet att vänta och lyssna på externa händelser. Den här funktionen [med varaktiga funktioner](durable-functions-overview.md) är ofta användbar för hantering av mänsklig interaktion eller andra externa utlösare.

> [!NOTE]
> Externa händelser är enkelriktade asynkrona åtgärder. De är inte lämpliga för situationer där klienten som skickar händelsen behöver ett synkront svar från orchestrator-funktionen.

## <a name="wait-for-events"></a>Vänta på händelser

(.NET) `WaitForExternalEvent` och `waitForExternalEvent` (JavaScript) metoderna för [orchestration-utlösarbindningen](durable-functions-bindings.md#orchestration-trigger) gör att en orchestrator-funktion asynkront väntar och lyssnar efter en extern händelse. Funktionen lyssningsorkesorkare deklarerar *namnet* på händelsen och *formen på de data som den* förväntar sig att ta emot.

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
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Du kan lyssna efter flera händelser samtidigt, till exempel i följande exempel, som väntar på en av tre möjliga händelsemeddelanden.

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
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

I föregående exempel lyssnar du efter *någon* av flera händelser. Det är också möjligt att vänta på *alla* händelser.

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
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

Om händelsenyttolasten inte kan konverteras till den `T`förväntade typen i .NET genereras ett undantag.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

`WaitForExternalEvent`väntar på obestämd tid för lite inmatning.  Funktionsappen kan tas bort på ett säkert sätt medan du väntar. Om och när en händelse anländer för den här orchestration-instansen väcks händelsen automatiskt och bearbetar händelsen omedelbart.

> [!NOTE]
> Om din funktionsapp använder förbrukningsplanen uppstår inga faktureringsavgifter medan en orchestrator-funktion väntar på en uppgift från `WaitForExternalEvent` (.NET) eller `waitForExternalEvent` (JavaScript), oavsett hur länge den väntar.

## <a name="send-events"></a>Skicka händelser

Metoden `RaiseEventAsync` (.NET) `raiseEvent` eller (JavaScript) för [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) skickar de händelser som `WaitForExternalEvent` (.NET) eller `waitForExternalEvent` (JavaScript) väntar på.  Metoden `RaiseEventAsync` tar *eventName* och *eventData* som parametrar. Händelsedata måste vara JSON-serialiserbara.

Nedan finns en exempelköutlöst funktion som skickar en "Godkännande"-händelse till en orchestrator-funktionsinstans. Orkestreringsinstans-ID:et kommer från brödtexten i kömeddelandet.

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
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Internt, `RaiseEventAsync` (.NET) `raiseEvent` eller (JavaScript) följer ett meddelande som plockas upp av den väntande orchestrator-funktionen. Om instansen inte väntar på det angivna *händelsenamnet* läggs händelsemeddelandet till i en minneskö. Om orkestreringsinstansen senare börjar lyssna efter *händelsenamnet* kontrollerar den kön efter händelsemeddelanden.

> [!NOTE]
> Om det inte finns någon orkestreringsinstans med det angivna *instans-ID:et*ignoreras händelsemeddelandet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar felhantering](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på mänsklig interaktion](durable-functions-phone-verification.md)