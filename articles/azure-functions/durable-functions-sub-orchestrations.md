---
title: Underordnade orkestreringar för varaktiga funktioner – Azure
description: Hur du anropar orkestreringar från orkestreringar i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 59e8eb41b7e9fe3d57196f6844d1a768c3ef598b
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094447"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Underordnade orkestreringar i varaktiga funktioner (Azure Functions)

Förutom anropar Aktivitetsfunktioner kan orchestrator-funktioner anropa andra orchestrator-funktioner. Du kan till exempel skapa en större dirigering av ett bibliotek av orchestrator-funktioner. Eller du kan köra flera instanser av en orchestrator-funktion parallellt.

En orchestrator-funktion kan anropa en annan orchestrator-funktion genom att anropa den [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) eller [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metod. Den [felhantering & kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) artikeln innehåller mer information om automatiska återförsök.

Underordnade orchestrator-funktioner fungerar precis som Aktivitetsfunktioner från anroparens perspektiv. De kan returnera ett värde i ett undantag och kan vara slutförts av överordnade orchestrator-funktion.

> [!NOTE]
> Den `CallSubOrchestratorAsync` och `CallSubOrchestratorWithRetryAsync` metoder är ännu inte tillgängliga i JavaScript.

## <a name="example"></a>Exempel

I följande exempel visas ett scenario för IoT (”Internet of Things”) där det finns flera enheter som måste etableras. Det finns en viss orchestration att måste ske för var och en av de enheter som kan se ut ungefär så här:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Den här orchestrator-funktion som kan användas som – är oneoff enhetsetablering eller det kan vara en del av en större dirigering. I det senare fallet överordnade orchestrator-funktion kan schemalägga instanser av `DeviceProvisioningOrchestration` med hjälp av den `CallSubOrchestratorAsync` API.

Här är ett exempel som visar hur du kör flera orchestrator-funktioner parallellt.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig vilka uppgiftshubbar är och hur du konfigurerar dem.](durable-functions-task-hubs.md)
