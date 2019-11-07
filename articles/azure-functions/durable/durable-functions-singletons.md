---
title: Singleton för Durable Functions – Azure
description: Använda singleton i Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ec7eb1eba2bc029d592560b39cde20e93e5afcd6
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614668"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Dirigerare i Durable Functions (Azure Functions)

För bakgrunds jobb måste du ofta se till att endast en instans av en viss Orchestrator körs i taget. Du kan se till att den här typen av singleton-beteende i [Durable Functions](durable-functions-overview.md) genom att tilldela ett särskilt instans-ID till en Orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

I följande exempel visas en funktion för HTTP-utlösare som skapar en singleton-dirigering av bakgrunds jobb. Koden ser till att det bara finns en instans för ett angivet instans-ID.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Föregående C# kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `OrchestrationClient` attribut i stället för attributet `DurableClient` och du måste använda `DurableOrchestrationClient` parameter typ i stället för `IDurableOrchestrationClient`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

Här är function. JSON-filen:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Här är JavaScript-koden:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Instans-ID: n är som standard slumpmässigt genererade GUID. I föregående exempel skickas instans-ID: t i flödes data från URL: en. Koden anropar `GetStatusAsync`(C#) eller `getStatus` (Java Script) för att kontrol lera om en instans med det angivna ID: t redan körs. Om ingen sådan instans körs skapas en ny instans med detta ID.

> [!NOTE]
> Det finns ett möjligt konkurrens villkor i det här exemplet. Om två instanser av **HttpStartSingle** körs samtidigt, rapporterar båda funktions anropen att de lyckas, men endast en Dirigerings instans kommer att starta. Detta kan ha oönskade sido effekter, beroende på dina behov. Därför är det viktigt att se till att det inte finns två begär Anden som kan köra den här utlösnings funktionen samtidigt.

Implementerings informationen för Orchestrator-funktionen spelar ingen roll. Det kan vara en vanlig Orchestrator-funktion som startar och slutförs, eller så kan det vara en som kör för alltid (det vill säga ett [Eternal-Orchestration](durable-functions-eternal-orchestrations.md)). Den viktiga punkten är att det bara finns en instans som körs i taget.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om de ursprungliga HTTP-funktionerna i Orchestration](durable-functions-http-features.md)
