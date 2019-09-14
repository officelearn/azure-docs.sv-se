---
title: Under dirigering för Durable Functions – Azure
description: Så här anropar du dirigeringar från dirigering i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: 7b5e811daecbb7687abe7a37b75e2730d7830c2c
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983621"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Under dirigering i Durable Functions (Azure Functions)

Förutom att anropa aktivitets funktioner kan Orchestrator-funktioner anropa andra Orchestrator-funktioner. Du kan till exempel bygga ett större dirigering från ett bibliotek med Orchestrator-funktioner. Du kan också köra flera instanser av en Orchestrator-funktion parallellt.

En Orchestrator-funktion kan anropa en annan Orchestrator-funktion genom att anropa [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) `callSubOrchestrator` [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) -eller CallSubOrchestratorWithRetryAsync-metoderna i .net `callSubOrchestratorWithRetry` , eller eller-metoderna i Java Script. Artikeln [fel hantering &s kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) innehåller mer information om automatiskt återförsök.

Under Orchestrator-funktioner fungerar precis som aktivitets funktioner från anroparens perspektiv. De kan returnera ett värde, utlösa ett undantag och kan förväntas av den överordnade Orchestrator-funktionen. 

> [!NOTE]
> För närvarande är det nödvändigt att ange ett `instanceId` argument värde för under Dirigerings-API: et i Java Script.

## <a name="example"></a>Exempel

I följande exempel illustreras ett IoT-scenario ("Sakernas Internet") där det finns flera enheter som måste tillhandahållas. Det finns ett visst dirigering som måste ske för varje enhet, vilket kan se ut ungefär så här:

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

Den här Orchestrator-funktionen kan användas som-är för en-off-enhets etablering eller kan ingå i ett större Dirigerings steg. I det senare fallet kan den överordnade Orchestrator-funktionen schemalägga instanser `DeviceProvisioningOrchestration` av `CallSubOrchestratorAsync` med hjälpC#av ( `callSubOrchestrator` ) eller (Java Script) API: et.

Här är ett exempel som visar hur du kör flera Orchestrator-funktioner parallellt.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

> [!NOTE]
> Under dirigering måste definieras i samma Function-app som den överordnade samordningen. Om du behöver anropa och vänta på dirigeringar i en annan Function-app, bör du överväga att använda det inbyggda stödet för HTTP API: er och HTTP 202-avsökningens konsument mönster. Mer information finns i avsnittet om [http-funktioner](durable-functions-http-features.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anger en anpassad Dirigerings status](durable-functions-custom-orchestration-status.md)