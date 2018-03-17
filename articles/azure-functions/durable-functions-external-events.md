---
title: "Hantera externa händelser i varaktiga funktioner – Azure"
description: "Lär dig mer om att hantera externa händelser i tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5ffbe6a7d74f0be2193d711d304f19e62ab08741
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i varaktiga funktioner (Azure-funktioner)

Orchestrator-funktioner har möjlighet att vänta och lyssna efter externa händelser. Den här funktionen av [varaktiga funktioner](durable-functions-overview.md) ofta är användbart för att hantera mänsklig interaktion eller andra externa utlösare.

## <a name="wait-for-events"></a>Vänta tills händelser

Den [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoden gör att en orchestrator funktion asynkront vänta och lyssna efter en extern händelse. Funktionen lyssnande orchestrator förklarar den *namn* för händelsen och *form av data* den förväntar sig att ta emot.

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

Föregående exempel lyssnar efter en viss händelse och vidtar åtgärder när den tas emot.

Du kan lyssna efter flera händelser samtidigt, precis som i följande exempel som väntar på en av tre möjliga händelsemeddelanden.

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

Föregående exempel lyssnar efter *alla* flera händelser. Det är också möjligt att vänta tills *alla* händelser.

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) väntar på obestämd tid för indata.  Funktionsapp kan tas bort från minnet väntan på ett säkert sätt. Om och när en händelse anländer för den här orchestration-instansen är aktiveras automatiskt och omedelbart bearbetar händelsen.

> [!NOTE]
> Om funktionen appen använder förbrukning planera, inga faktureringskostnader medan en orchestrator-funktion väntar på en aktivitet från `WaitForExternalEvent`, oavsett hur länge den väntar.

Om händelsenyttolasten inte kan konverteras till den förväntade typen `T`, genereras ett undantag.

## <a name="send-events"></a>Skicka händelser

Den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metod för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen skickar händelser som `WaitForExternalEvent` väntar.  Den `RaiseEventAsync` metoden tar *eventName* och *eventData* som parametrar. Informationen om händelsen måste vara JSON-serialiserbara.

Nedan visas ett exempel kön funktion som utlöses som skickar en ”godkännande”-händelse till en instans för orchestrator-funktionen. Orchestration-instans-ID kommer från kön postmeddelandets brödtext.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Internt `RaiseEventAsync` enqueues ett meddelande som hämtar plockats av funktionen väntar orchestrator.

> [!WARNING]
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamnet*, händelsemeddelandet ignoreras. Mer information om det här problemet finns på [GitHub problemet](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du ställer in eternal orkestreringarna](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på att externa händelser](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Kör ett exempel som väntar på mänsklig interaktion](durable-functions-phone-verification.md)

