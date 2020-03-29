---
title: Eviga orkestreringar i varaktiga funktioner - Azure
description: Lär dig hur du implementerar eviga orkestreringar med hjälp av tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 572fec4d6e47efd734bc84a40dc974c79bd619fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262987"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eviga orkestreringar i varaktiga funktioner (Azure-funktioner)

*Eviga orkestreringar* är orchestrator funktioner som aldrig tar. De är användbara när du vill använda [varaktiga funktioner](durable-functions-overview.md) för aggregatorer och alla scenarier som kräver en oändlig loop.

## <a name="orchestration-history"></a>Orkestrering historia

Som förklaras i [orchestration historia](durable-functions-orchestrations.md#orchestration-history) ämnet håller durable task framework reda på historien om varje funktion orkestrering. Den här historien växer kontinuerligt så länge orchestrator-funktionen fortsätter att schemalägga nytt arbete. Om orchestrator-funktionen går in i en oändlig loop och kontinuerligt scheman fungerar, kan den här historiken växa kritiskt stor och orsaka betydande prestandaproblem. Det *eviga orkestreringskonceptet* har utformats för att minska dessa typer av problem för program som behöver oändliga loopar.

## <a name="resetting-and-restarting"></a>Återställa och starta om

I stället för att använda oändliga loopar återställer orchestrator-funktioner sitt tillstånd genom att anropa `ContinueAsNew` metoden (.NET) eller `continueAsNew` (JavaScript) för [orchestration-utlösarbindningen](durable-functions-bindings.md#orchestration-trigger). Den här metoden tar en enda JSON-serialisable parameter, som blir den nya indata för nästa orchestrator funktion generation.

När `ContinueAsNew` anropas, instansen följer ett meddelande till sig själv innan den avslutas. Meddelandet startar om instansen med det nya indatavärdet. Samma instans-ID behålls, men orchestrator-funktionens historik trunkeras effektivt.

> [!NOTE]
> Durable Task Framework underhåller samma instans-ID men skapar internt ett nytt *körnings-ID* för orchestrator-funktionen som återställs av `ContinueAsNew`. Det här körnings-ID:et visas i allmänhet inte externt, men det kan vara bra att känna till när du felsöker orkestertionkörning.

## <a name="periodic-work-example"></a>Exempel på periodiskt arbete

Ett användningsfall för eviga orkestreringar är kod som behöver göra periodiskt arbete på obestämd tid.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Det föregående C#-exemplet är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Skillnaden mellan det här exemplet och en timer-utlöst funktion är att rensningsutlösare gånger här inte baseras på ett schema. Ett CRON-schema som kör en funktion varje timme kör det till exempel klockan 1:00, 2:00, 3:00 etc. och kan eventuellt stöta på överlappningsproblem. I det här exemplet, om rensningen tar 30 minuter, kommer den att schemaläggas klockan 1:00, 2:30, 4:00, etc. och det finns ingen risk för överlappning.

## <a name="starting-an-eternal-orchestration"></a>Starta en evig orkestrering

Använd `StartNewAsync` metoden (.NET) `startNew` eller (JavaScript) för att starta en evig orkestrering, precis som du skulle ha någon annan orkestreringsfunktion.  

> [!NOTE]
> Om du behöver se till att en evig orkestrering körs `id` är det viktigt att behålla samma instans när orchestration startas. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Avsluta från en evig orkestrering

Om en orchestrator-funktion måste slutföras så småningom *not* behöver `ContinueAsNew` du inte anropa och låta funktionen avslutas.

Om en orchestrator-funktion finns i en oändlig loop `TerminateAsync` och måste `terminate` stoppas använder du metoden (.NET) eller (JavaScript) för [att stoppa](durable-functions-bindings.md#orchestration-client) den. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar singleton orchestrations](durable-functions-singletons.md)
