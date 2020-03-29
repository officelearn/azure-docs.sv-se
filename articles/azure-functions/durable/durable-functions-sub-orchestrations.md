---
title: Underorkestreringar för varaktiga funktioner - Azure
description: Anropa orkestreringar från orkestreringar i tillägget Varaktiga funktioner för Azure-funktioner.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261525"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Underorkestreringar i varaktiga funktioner (Azure-funktioner)

Förutom att anropa aktivitetsfunktioner kan orchestrator-funktioner anropa andra orchestrator-funktioner. Du kan till exempel skapa en större orkestrering från ett bibliotek med mindre orchestrator-funktioner. Du kan också köra flera instanser av en orchestrator-funktion parallellt.

En orchestrator-funktion kan anropa en `CallSubOrchestratorAsync` annan `CallSubOrchestratorWithRetryAsync` orchestrator-funktion med `callSubOrchestrator` `callSubOrchestratorWithRetry` metoderna i .NET eller metoderna eller i JavaScript. Artikeln [Felhantering & Kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) innehåller mer information om automatiskt återförsök.

Underorkesorkesfunktioner fungerar precis som aktivitetsfunktioner ur anroparens perspektiv. De kan returnera ett värde, kasta ett undantag och kan inväntas av den överordnade orchestrator-funktionen. 
## <a name="example"></a>Exempel

Följande exempel illustrerar ett IoT-scenario ("Sakernas Internet") där det finns flera enheter som måste etableras. Följande funktion representerar det etableringsarbetsflöde som måste köras för varje enhet:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Den här orchestrator-funktionen kan användas som den är för engångsenhetsetablering eller så kan den ingå i en större orkestrering. I det senare fallet kan den överordnade orchestrator-funktionen schemalägga instanser av `DeviceProvisioningOrchestration` att använda API:et `CallSubOrchestratorAsync` (.NET) eller `callSubOrchestrator` (JavaScript).

Här är ett exempel som visar hur du kör flera orchestrator-funktioner parallellt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De tidigare C#-exemplen är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Underorkestreringar måste definieras i samma funktionsapp som den överordnade orkestreringen. Om du behöver ringa och vänta på orkestreringar i en annan funktionsapp kan du överväga att använda det inbyggda stödet för HTTP-API:er och http 202-avsökningskonsumentmönstret. Mer information finns i avsnittet [HTTP-funktioner.](durable-functions-http-features.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du anger en anpassad orkestreringsstatus](durable-functions-custom-orchestration-status.md)