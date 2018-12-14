---
title: Singletons för varaktiga funktioner – Azure
description: Hur du använder singletons i tillägget varaktiga Functons för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: b083b9a09b478ca5ad68e19d3a2133fb529da851
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342960"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-initierare i varaktiga funktioner (Azure Functions)

För bakgrundsjobb eller aktör-style-orkestreringar du ofta behöva se till att endast en instans av körs en viss orchestrator samtidigt. Detta kan göras [varaktiga funktioner](durable-functions-overview.md) genom att tilldela en specifik instans-ID till en orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

Följande C# och JavaScript-exemplen visar en HTTP-utlösare-funktion som skapar en singleton bakgrund jobborkestrering. Koden ser till att endast en instans som finns för ett angivet instans-ID.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

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

Som standard genereras instans-ID: N är slumpmässigt GUID. Men i det här fallet instans-ID skickas i dirigera data från URL: en. Koden anropar [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) eller `getStatus` (JavaScript) för att kontrollera om en instans med det angivna ID: T redan körs. Om inte, skapa en instans med detta ID.

> [!WARNING]
> När du utvecklar lokalt i JavaScript, behöver du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t.ex. `localhost:7071` att använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Det finns ett potentiella konkurrenstillstånd i det här exemplet. Om två instanser av **HttpStartSingle** kör du samtidigt, resultatet kunde två olika skapas instanser av singleton, något som skriver över den andra. Beroende på dina krav kan detta få oönskade sidoeffekter. Därför är det viktigt att se till att inga två begäranden kan köra den här Utlösarfunktion samtidigt.

Implementeringsinformationen om orchestrator-funktion faktiskt spelar roll. Det kan vara en vanlig orchestrator-funktion som startas och slutförs eller ett som körs alltid (det vill säga en [Eternal Orchestration](durable-functions-eternal-orchestrations.md)). Det viktiga är att det finns endast en enda instans som körs på en gång.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anropar underordnade orkestreringar](durable-functions-sub-orchestrations.md)
