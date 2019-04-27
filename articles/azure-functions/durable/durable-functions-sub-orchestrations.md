---
title: Underordnade orkestreringar för varaktiga funktioner – Azure
description: Hur du anropar orkestreringar från orkestreringar i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: 1ab9a5714a7ef24b51957bd48b1b67240cf13adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730250"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Underordnade orkestreringar i varaktiga funktioner (Azure Functions)

Förutom anropar Aktivitetsfunktioner kan orchestrator-funktioner anropa andra orchestrator-funktioner. Du kan till exempel skapa en större dirigering av ett bibliotek av orchestrator-funktioner. Eller du kan köra flera instanser av en orchestrator-funktion parallellt.

En orchestrator-funktion kan anropa en annan orchestrator-funktion genom att anropa den [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) eller [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metoder i .NET, eller `callSubOrchestrator` eller `callSubOrchestratorWithRetry` metoder i JavaScript. Den [felhantering & kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) artikeln innehåller mer information om automatiska återförsök.

Underordnade orchestrator-funktioner fungerar precis som Aktivitetsfunktioner från anroparens perspektiv. De kan returnera ett värde i ett undantag och kan vara slutförts av överordnade orchestrator-funktion.

## <a name="example"></a>Exempel

I följande exempel visas ett scenario för IoT (”Internet of Things”) där det finns flera enheter som måste etableras. Det finns en viss orchestration att måste ske för var och en av de enheter som kan se ut ungefär så här:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Den här orchestrator-funktion som kan användas som – är oneoff enhetsetablering eller det kan vara en del av en större dirigering. I det senare fallet överordnade orchestrator-funktion kan schemalägga instanser av `DeviceProvisioningOrchestration` med hjälp av den `CallSubOrchestratorAsync` (C#) eller `callSubOrchestrator` (JavaScript) API.

Här är ett exempel som visar hur du kör flera orchestrator-funktioner parallellt.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig vilka uppgiftshubbar är och hur du konfigurerar dem.](durable-functions-task-hubs.md)

