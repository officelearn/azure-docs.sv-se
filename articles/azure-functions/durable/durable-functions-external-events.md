---
title: Hantera externa händelser i varaktiga funktioner – Azure
description: Lär dig hur du hanterar externa händelser i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 98380cc5b9daff314283ac4e45e5edf7b5601e1b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642300"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i varaktiga funktioner (Azure Functions)

Orchestrator-funktioner har möjlighet att vänta och lyssna efter externa händelser. Den här funktionen i [varaktiga funktioner](durable-functions-overview.md) kan ofta vara användbart för hantering av mänsklig interaktion eller andra externa utlösare.

## <a name="wait-for-events"></a>Vänta på händelser

Den [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoden kan en orchestrator-funktion att vänta asynkront och lyssna efter en extern händelse. Funktionen lyssnande orchestrator deklarerar den *namn* händelsens och *formen* det förväntar sig att ta emot.

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

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

I föregående exempel lyssnar efter en viss enskild händelse och vidtar åtgärder när den tas emot.

Du kan lyssna efter flera händelser samtidigt, precis som i följande exempel, som väntar på en av tre möjliga händelsemeddelanden.

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

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

Det tidigare exemplet lyssnar efter *alla* av flera händelser. Det är också möjligt att vänta tills *alla* händelser.

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) väntar på obestämd tid tills några indata.  Funktionsappen kan tas bort från minnet väntan på ett säkert sätt. Om en händelse tas emot för den här orchestration-instansen, och när den aktiveras installationen automatiskt och omedelbart bearbetar händelsen.

> [!NOTE]
> Om din funktionsapp använder den Standardförbrukningsplanen kan inga fakturering avgifter tillkommer när en uppgift från väntar på en orchestrator-funktion `WaitForExternalEvent`, oavsett hur länge den ska vänta.

I .NET, om händelsenyttolast inte kan konverteras till den förväntade typen `T`, genereras ett undantagsfel.

## <a name="send-events"></a>Skicka händelser

Den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass skickar händelser som `WaitForExternalEvent` väntar.  Den `RaiseEventAsync` metoden tar *eventName* och *eventData* som parametrar. Informationen om händelsen måste vara JSON-serialiserbara.

Nedan visas ett exempel kö-utlöst funktion som skickar en ”godkännande”-händelse till en instans för orchestrator-funktion. Orchestration-instans-ID kommer från brödtexten i kömeddelandet.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)
I JavaScript har vi att anropa en rest api för att utlösa en händelse som funktionen beständiga väntar.
Koden nedan använder ”-begäran”-paket. Metoden nedan kan användas för att generera en händelse för alla hållbara funktion

```js
function raiseEvent(instanceId, eventName) {
        var url = `<<BASE_URL>>/runtime/webhooks/durabletask/instances/${instanceId}/raiseEvent/${eventName}?taskHub=DurableFunctionsHub`;
        var body = <<BODY>>
            
        return new Promise((resolve, reject) => {
            request({
                url,
                json: body,
                method: "POST"
            }, (e, response) => {
                if (e) {
                    return reject(e);
                }

                resolve();
            })
        });
    }
```

<< BASE_URL >> blir bas-url i din funktionsapp. Om du kör kod lokalt och sedan den ser ut ungefär så http://localhost:7071 eller i Azure som https://<<functionappname>>. azurewebsites.net


Internt `RaiseEventAsync` placerar det i kö ett meddelande som hämtar slutpunktsstatus uppfattas av orchestrator-funktion för att vänta.

> [!WARNING]
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamn*, händelsemeddelandet ignoreras. Mer information om det här problemet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du ställer in eternal-orkestreringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Köra ett exempel som väntar på externa händelser](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Köra ett exempel som väntar mänsklig interaktion](durable-functions-phone-verification.md)

