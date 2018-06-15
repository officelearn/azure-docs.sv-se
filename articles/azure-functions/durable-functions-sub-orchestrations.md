---
title: Underordnade orkestreringarna för beständig funktioner – Azure
description: Hur du anropar orkestreringarna från orkestreringarna i tillägget varaktiga funktioner för Azure Functions.
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
ms.openlocfilehash: 7545a371749ed9af88f08af23cce3a513f494374
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33761341"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Underordnade orkestreringarna i varaktiga funktioner (Azure-funktioner)

Orchestrator-funktioner kan anropa andra orchestrator-funktioner utöver anropar aktivitet funktioner. Du kan till exempel skapa en större orchestration utanför ett bibliotek med orchestrator-funktioner. Eller så kan du köra flera instanser av en orchestrator-funktion parallellt.

En orchestrator-funktion kan anropa en annan funktion i orchestrator genom att anropa den [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) eller [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metod. Den [felhantering & kompensation](durable-functions-error-handling.md#automatic-retry-on-failure) artikeln innehåller mer information om automatiska försök igen.

Underordnade orchestrator-funktioner fungerar precis som aktiviteten funktioner från anroparens perspektiv. De kan returnera ett värde, utlöser ett undantag och kan inväntas av funktionen överordnade orchestrator.

> [!NOTE]
> Den `CallSubOrchestratorAsync` och `CallSubOrchestratorWithRetryAsync` metoder är ännu inte tillgängliga i JavaScript.

## <a name="example"></a>Exempel

Följande exempel illustrerar ett scenario för IoT (”Sakernas Internet”) där det finns flera enheter som ska etableras. Det finns en viss orchestration som måste utföras för alla enheter, vilket kan se ut ungefär så här:

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

Den här funktionen för orchestrator kan användas som-är oneoff enhetsetableringen eller det kan vara en del av en större orchestration. I det senare fallet funktionen överordnade orchestrator kan schemalägga instanser av `DeviceProvisioningOrchestration` med hjälp av den `CallSubOrchestratorAsync` API.

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
> [Lär dig vad uppgiften hubbar är och hur du konfigurerar dem](durable-functions-task-hubs.md)
