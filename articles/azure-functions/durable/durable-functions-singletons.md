---
title: Singleton för Durable Functions – Azure
description: Använda singleton i Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9bf9687f60e649fee98869ef263117177ad5efd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097936"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Dirigerare i Durable Functions (Azure Functions)

För bakgrunds jobb behöver du ofta se till att endast en instans av en viss Orchestrator körs i taget. Detta kan göras i [Durable Functions](durable-functions-overview.md) genom att tilldela ett särskilt instans-ID till en Orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

Följande C# och JavaScript-exempel visar en funktion för http-utlösare som skapar en singleton-dirigering för bakgrunds jobb. Koden ser till att det bara finns en instans för ett angivet instans-ID.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

Här är filen function.json:
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

Här är JavaScript-kod:
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

Instans-ID: n är som standard slumpmässigt genererade GUID. Men i det här fallet skickas instans-ID: t i flödes data från URL: en. Koden anropar [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) eller `getStatus` (Java Script) för att kontrol lera om en instans med det angivna ID: t redan körs. Om inte, skapas en instans med detta ID.

> [!WARNING]
> När du utvecklar lokalt i Java Script måste du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t. ex. `localhost:7071`använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i [GitHub-problemet](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Det finns ett möjligt konkurrens villkor i det här exemplet. Om två instanser av **HttpStartSingle** körs samtidigt, rapporterar båda funktions anropen att de lyckas, men endast en Dirigerings instans kommer att starta. Detta kan ha oönskade sido effekter, beroende på dina behov. Därför är det viktigt att se till att det inte finns två begär Anden som kan köra den här utlösnings funktionen samtidigt.

Implementerings informationen för Orchestrator-funktionen spelar ingen roll. Det kan vara en vanlig Orchestrator-funktion som startar och slutförs, eller så kan det vara en som kör för alltid (det vill säga ett [Eternal-Orchestration](durable-functions-eternal-orchestrations.md)). Den viktiga punkten är att det bara finns en instans som körs i taget.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om hur du anropar under-Orchestration](durable-functions-sub-orchestrations.md)
