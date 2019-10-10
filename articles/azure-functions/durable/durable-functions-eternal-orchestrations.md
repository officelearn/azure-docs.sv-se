---
title: Eternal-dirigering i Durable Functions – Azure
description: Lär dig hur du implementerar Eternal-dirigering med hjälp av Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d96229bb5e3d288915b64e5a7ce29a8651f2a181
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177383"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal-dirigeringar i Durable Functions (Azure Functions)

*Eternal-dirigering* är Orchestrator-funktioner som aldrig slutar. De är användbara när du vill använda [Durable Functions](durable-functions-overview.md) för agg regeringar och alla scenarier som kräver en oändlig loop.

## <a name="orchestration-history"></a>Orchestration-historik

Som förklaras i avsnittet om [Orchestration-historik](durable-functions-orchestrations.md#orchestration-history) , håller det varaktiga aktivitets ramverket att spåra historiken för varje funktions dirigering. Den här historiken växer kontinuerligt så länge Orchestrator-funktionen fortsätter att schemalägga nytt arbete. Om Orchestrator-funktionen hamnar i en oändlig slinga och kontinuerligt schemalägger arbete, kan den här historiken växa mycket stor och orsaka betydande prestanda problem. *Eternal Orchestration* -konceptet har utformats för att minimera dessa typer av problem för program som behöver oändliga slingor.

## <a name="resetting-and-restarting"></a>Återställa och starta om

I stället för att använda oändliga slingor återställs deras tillstånd genom att [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) -metoden anropas. Den här metoden tar en enda JSON-serialiserbar parameter, som blir den nya ingången för nästa generations funktion i Orchestrator.

När `ContinueAsNew` anropas, kommer instansen att köa ett meddelande innan det avslutas. Meddelandet startar om instansen med det nya indatavärdet. Samma instans-ID behålls, men Orchestrator-funktionens historik trunkeras effektivt.

> [!NOTE]
> Det beständiga aktivitets ramverket upprätthåller samma instans-ID men skapar internt ett nytt *körnings-ID* för Orchestrator-funktionen som återställs av `ContinueAsNew`. Detta körnings-ID visas normalt inte externt, men det kan vara användbart att känna till vid fel sökning av Orchestration-körning.

## <a name="periodic-work-example"></a>Exempel på periodiskt arbete

Ett användnings fall för Eternal-dirigering är kod som behöver göra periodiskt arbete på obestämd tid.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Skillnaden mellan det här exemplet och en timer-utlöst funktion är att rensnings utlösare här inte baseras på ett schema. Ett CRON-schema som kör en funktion varje timme kommer till exempel att köra den på 1:00, 2:00, 3:00 osv. och kan eventuellt leda till överlappande problem. I det här exemplet, men om rensningen tar 30 minuter, kommer den att schemaläggas till 1:00, 2:30, 4:00 osv. det finns ingen risk för överlappande.

## <a name="starting-an-eternal-orchestration"></a>Starta en Eternal-dirigering
Använd metoden [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) för att starta en Eternal-dirigering. Detta är inte något annat än att utlösa någon annan Orchestration-funktion.  

> [!NOTE]
> Om du behöver se till att en singleton-Eternal-Orchestration körs är det viktigt att du underhåller samma instans `id` när du startar dirigeringen. Mer information finns i [instans hantering](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Avsluta från en Eternal-dirigering

Om en Orchestrator-funktion behöver slutföras måste du *inte* anropa `ContinueAsNew` och låta funktionen avslutas.

Om en Orchestrator-funktion finns i en oändlig loop och måste stoppas, använder du [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) -metoden för att stoppa den. Mer information finns i [instans hantering](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar singleton-dirigeringar](durable-functions-singletons.md)
