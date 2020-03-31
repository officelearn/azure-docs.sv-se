---
title: Singletons för varaktiga funktioner - Azure
description: Så här använder du singletons i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262817"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton orchestrators i varaktiga funktioner (Azure Functions)

För bakgrundsjobb måste du ofta se till att endast en instans av en viss orchestrator körs åt gången. Du kan säkerställa den här typen av singleton-beteende i [varaktiga funktioner](durable-functions-overview.md) genom att tilldela ett specifikt instans-ID till en orchestrator när du skapar det.

## <a name="singleton-example"></a>Singleton exempel

I följande exempel visas en HTTP-trigger-funktion som skapar en singleton-bakgrundsjobborkestrering. Koden säkerställer att det bara finns en instans för ett angivet instans-ID.

# <a name="c"></a>[C#](#tab/csharp)

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
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

**funktion.json**

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

**index.js**

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

---

Som standard genereras instans-ID slumpmässigt GUID:er. I föregående exempel skickas dock instans-ID:t i flödesdata från URL:en. Koden anropar `GetStatusAsync`(C#) eller `getStatus` (JavaScript) för att kontrollera om en instans med angivet ID redan körs. Om ingen sådan instans körs skapas en ny instans med det ID:t.

> [!NOTE]
> Det finns en potentiell ras villkor i detta prov. Om två instanser av **HttpStartSingle** körs samtidigt kommer båda funktionsanropen att rapportera framgång, men bara en orchestration-instans startar faktiskt. Beroende på dina krav, Detta kan ha oönskade biverkningar. Därför är det viktigt att se till att inga två begäranden kan köra den här utlösarfunktionen samtidigt.

Implementeringsdetaljerna för orchestrator-funktionen spelar egentligen ingen roll. Det kan vara en vanlig orchestrator funktion som startar och slutförs, eller det kan vara en som löper för evigt (det vill säga en [evig orkestrering).](durable-functions-eternal-orchestrations.md) Det viktiga är att det bara finns en instans i taget.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om de inbyggda HTTP-funktionerna i orkestreringar](durable-functions-http-features.md)
