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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 8177fb6e8dea83ab2b8b12183cdcca6e43f92ade
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095104"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-initierare i varaktiga funktioner (Azure Functions)

För bakgrundsjobb eller aktör-style-orkestreringar du ofta behöva se till att endast en instans av körs en viss orchestrator samtidigt. Detta kan göras [varaktiga funktioner](durable-functions-overview.md) genom att tilldela en specifik instans-ID till en orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

I följande C#-exempel visas en HTTP-utlösare-funktion som skapar en singleton bakgrund jobborkestrering. Koden ser till att endast en instans som finns för ett angivet instans-ID.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
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

Som standard genereras instans-ID: N är slumpmässigt GUID. Men i det här fallet instans-ID skickas i dirigera data från URL: en. Koden anropar [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) att kontrollera om en instans med det angivna ID: T redan körs. Om inte, skapa en instans med detta ID.

> [!NOTE]
> Det finns ett potentiella konkurrenstillstånd i det här exemplet. Om två instanser av **HttpStartSingle** kör du samtidigt, resultatet kunde två olika skapas instanser av singleton, något som skriver över den andra. Beroende på dina krav kan detta få oönskade sidoeffekter. Därför är det viktigt att se till att inga två begäranden kan köra den här Utlösarfunktion samtidigt.

Implementeringsinformationen om orchestrator-funktion faktiskt spelar roll. Det kan vara en vanlig orchestrator-funktion som startas och slutförs eller ett som körs alltid (det vill säga en [Eternal Orchestration](durable-functions-eternal-orchestrations.md)). Det viktiga är att det finns endast en enda instans som körs på en gång.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anropar underordnade orkestreringar](durable-functions-sub-orchestrations.md)
