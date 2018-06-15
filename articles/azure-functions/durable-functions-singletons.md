---
title: Singletons för beständig funktioner – Azure
description: Hur du använder singletons i varaktiga Functons-tillägget för Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972559"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton orchestrators i varaktiga funktioner (Azure-funktioner)

För bakgrundsjobb eller aktören-format orkestreringarna du ofta måste du se till att endast en instans av körs en viss orchestrator samtidigt. Detta kan göras [varaktiga funktioner](durable-functions-overview.md) genom att tilldela en specifik instans du-ID till en orchestrator när du skapar den.

## <a name="singleton-example"></a>Singleton-exempel

I följande C#-exempel visas en HTTP-utlösare funktion som skapar en singleton bakgrund jobbet orchestration. Koden säkerställer att endast en instans som finns angivna instans-ID.

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

Som standard genereras instans-ID är ett slumpmässigt GUID. Men i det här fallet instans-ID skickas vidarebefordra data från URL: en. Koden anropar [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) att kontrollera en instans med angivet ID är redan igång. Om inte, skapa en instans med detta ID.

Implementeringsinformation om orchestrator-funktionen ingen faktiskt roll. Det kan vara en vanlig orchestrator-funktion som startas och är klart eller ett körs alltid (det vill säga en [Eternal Orchestration](durable-functions-eternal-orchestrations.md)). Det viktiga är att det finns endast någonsin en instans som körs på en gång.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anropar underordnade orkestreringarna](durable-functions-sub-orchestrations.md)
