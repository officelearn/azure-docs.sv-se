---
title: "Hantera instanser i varaktiga funktioner – Azure"
description: "Lär dig hur du hanterar instanser i tillägget varaktiga funktioner för Azure Functions."
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
ms.openlocfilehash: 399bad6f00b61d582fdb077f33000b6c55cf8904
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Hantera instanser i varaktiga funktioner (Azure-funktioner)

[Beständiga funktioner](durable-functions-overview.md) orchestration-instanser kan vara igång, avslutas, efterfrågas och skickas meddelandehändelser. Alla instanshantering görs med hjälp av den [orchestration klienten bindning](durable-functions-bindings.md). Den här artikeln gäller detaljer om varje instans management-åtgärd.

## <a name="starting-instances"></a>Starta instanser

Den [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) -metoden i den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) startar en ny instans av en orchestrator-funktion. Instanser av den här klassen kan erhållas med hjälp av den `orchestrationClient` bindning. Internt, den här metoden enqueues meddelandet i kön kontroll som utlöser start för en funktion med det angivna namnet som använder den `orchestrationTrigger` utlösa bindning.

Parametrarna är följande:

* **Namnet**: namnet på orchestrator-funktionen om du vill schemalägga.
* **Inkommande**: JSON-serialiserbara data som ska skickas som indata till orchestrator-funktionen.
* **InstanceId**: (valfritt) det unika ID: T för instansen. Om inget anges genereras ett slumpmässigt instans-ID.

Här är ett enkelt C#-exempel:

```csharp
[FunctionName("HelloWorldManualStart")]
public static Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

För icke-.NET-språk funktionen utdatabindning kan användas för att starta samt nya instanser. I det här fallet kan JSON-serialiserbara objekt som har ovanstående tre parametrar som fält användas. Tänk dig följande Node.js-funktion:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Vi rekommenderar att du använder en slumpmässig identifierare för instansens ID. Detta bidrar till ett lika belastningsdistribution vid skalning orchestrator funktioner över flera virtuella datorer. Rätt tid att använda icke-slumpmässiga instans-ID: N är när ID: T måste komma från en extern källa eller när du implementerar den [singleton orchestrator](durable-functions-singletons.md) mönster.

## <a name="querying-instances"></a>Frågar instanser

Den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) -metoden i den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen frågar efter statusen för en orchestration-instans. Det tar en `instanceId` som en parameter och returnerar ett objekt med följande egenskaper:

* **Namnet**: namnet på orchestrator-funktion.
* **InstanceId**: instans-ID för orchestration (ska vara samma som den `instanceId` indata).
* **CreatedTime**: den tid då funktionen orchestrator började köras.
* **LastUpdatedTime**: tidpunkt då orchestration senaste kontrollpunkt.
* **Inkommande**: indata för funktionen som ett JSON-värde.
* **Utdata**: resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades, tas den här egenskapen information om felet. Om orchestrator-funktionen har avslutats tas den här egenskapen angivna orsak till uppsägningen (eventuella).
* **RuntimeStatus**: ett av följande värden:
    * **Kör**: instansen har startats.
    * **Slutföra**: instansen har slutförts normalt.
    * **ContinuedAsNew**: instansen har startats om sig själv med en lista. Detta är ett tillfälligt tillstånd.
    * **Det gick inte**: instansen misslyckades med ett fel.
    * **Avslutas**: instansen avbröts tvärt.
    
Den här metoden returnerar `null` om instansen finns inte eller har inte startats än körs.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Instans-frågan är för närvarande stöds endast för C#-funktioner.

## <a name="terminating-instances"></a>Avslutande instanser

En instans som körs kan avslutas med hjälp av den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metod för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. De två parametrarna är en `instanceId` och en `reason` sträng som skrivs till loggarna och instansens status. En avslutade instans stoppas när den når nästa `await` plats, eller så avslutas omedelbart om det redan finns på en `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Instansen avslutning är för närvarande stöds endast för C#-funktioner.

## <a name="sending-events-to-instances"></a>Skickar händelser till instanser

Händelsemeddelanden kan skickas till kör instanser som använder den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metod för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. Instanser som kan hantera dessa händelser är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). Den angivna informationen är:

* **InstanceId**: unikt ID för instansen.
* **EventName**: namnet på händelsen att skicka.
* **EventData**: nyttolast för ett JSON-serialiserbara att skicka till instansen.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Höja händelser stöds för närvarande endast för C#-funktioner.

> [!WARNING]
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamnet*, händelsemeddelandet ignoreras. Mer information om det här problemet finns på [GitHub problemet](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder HTTP-APIs exempelvis management](durable-functions-http-api.md)
