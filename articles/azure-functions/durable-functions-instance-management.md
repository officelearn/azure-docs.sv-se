---
title: Hantera instanser i varaktiga funktioner – Azure
description: Lär dig hur du hanterar instanser i tillägget varaktiga funktioner för Azure Functions.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: d6f7c924491614190952ce620f33572307a22c22
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265443"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Hantera instanser i varaktiga funktioner (Azure-funktioner)

[Beständiga funktioner](durable-functions-overview.md) orchestration-instanser kan vara igång, avslutas, efterfrågas och skickas meddelandehändelser. Alla instanshantering görs med hjälp av den [orchestration klienten bindning](durable-functions-bindings.md). Den här artikeln gäller detaljer om varje instans management-åtgärd.

## <a name="starting-instances"></a>Starta instanser

Den [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) -metoden i den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) startar en ny instans av en orchestrator-funktion. Instanser av den här klassen kan erhållas med hjälp av den `orchestrationClient` bindning. Internt, den här metoden enqueues meddelandet i kön kontroll som utlöser start för en funktion med det angivna namnet som använder den `orchestrationTrigger` utlösa bindning. 

Uppgiften har slutförts när orchestration-processen startas. Orchestration-processen ska starta inom 30 sekunder. Om det tar längre tid, en `TimeoutException` genereras. 

Parametrarna för [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) är följande:

* **Namnet**: namnet på orchestrator-funktionen om du vill schemalägga.
* **Inkommande**: JSON-serialiserbara data som ska skickas som indata till orchestrator-funktionen.
* **InstanceId**: (valfritt) det unika ID: T för instansen. Om inget anges genereras ett slumpmässigt instans-ID.

Här är ett enkelt C#-exempel:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

För icke-.NET-språk funktionen utdatabindning kan användas för att starta samt nya instanser. I det här fallet kan JSON-serialiserbara objekt som har ovanstående tre parametrar som fält användas. Tänk dig följande JavaScript-funktion:

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

Den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) -metoden i den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen frågar efter statusen för en orchestration-instans. Det tar en `instanceId` (obligatoriskt), `showHistory` (valfritt) och `showHistoryOutput` (valfritt) som parametrar. Om `showHistory` är inställd på `true`, svaret innehåller historiken för körning. Om `showHistoryOutput` är inställd på `true` , körning historiken innehåller aktivitetsutdata. Metoden returnerar ett objekt med följande egenskaper:

* **Namnet**: namnet på orchestrator-funktion.
* **InstanceId**: instans-ID för orchestration (ska vara samma som den `instanceId` indata).
* **CreatedTime**: den tid då funktionen orchestrator började köras.
* **LastUpdatedTime**: tidpunkt då orchestration senaste kontrollpunkt.
* **Inkommande**: indata för funktionen som ett JSON-värde.
* **CustomStatus**: anpassad orchestration status i JSON-format. 
* **Utdata**: resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades, tas den här egenskapen information om felet. Om orchestrator-funktionen har avslutats tas den här egenskapen angivna orsak till uppsägningen (eventuella).
* **RuntimeStatus**: ett av följande värden:
    * **Kör**: instansen har startats.
    * **Slutföra**: instansen har slutförts normalt.
    * **ContinuedAsNew**: instansen har startats om sig själv med en lista. Detta är ett tillfälligt tillstånd.
    * **Det gick inte**: instansen misslyckades med ett fel.
    * **Avslutas**: instansen avbröts tvärt.
* **Historik**: körningstiden för orchestration. Det här fältet fylls endast om `showHistory` är inställd på `true`.
    
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
## <a name="querying-all-instances"></a>Frågar alla instanser

Du kan använda den `GetStatusAsync` metod för att fråga status för alla orchestration-instanser. Det tar inga parametrar eller skicka en `CancellationToken` objekt om du vill avbryta den. Metoden returnerar ett objekt med samma egenskaper som den `GetStatusAsync` metod med parametrar, förutom att det inte returnerar historik. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>Avslutande instanser

En instans som körs orchestration kan avslutas med hjälp av den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metod för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. De två parametrarna är en `instanceId` och en `reason` sträng som skrivs till loggarna och instansens status. En avslutade instans stoppas när den når nästa `await` plats, eller så avslutas omedelbart om det redan finns på en `await`. 

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
> Instansen avslutning sprida inte för närvarande. Aktiviteten funktioner och underordnade orkestreringarna körs klart oavsett om orchestration-instans som kallas dem har avslutats.

## <a name="sending-events-to-instances"></a>Skickar händelser till instanser

Händelsemeddelanden kan skickas till kör instanser som använder den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metod för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. Instanser som kan hantera dessa händelser är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Parametrarna för [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) är följande:

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

> [!WARNING]
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamnet*, händelsemeddelandet ignoreras. Mer information om det här problemet finns på [GitHub problemet](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Vänta på slutförande orchestration

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API som kan användas för att hämta synkront faktiska utdata från en orchestration-instans. Metoden använder standardvärdet 10 sekunder för `timeout` och 1 sekund för `retryInterval` när de har inte angetts.  

Här är ett exempel på HTTP-utlösare funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Funktionen kan anropas med följande rad med 2 sekunder timeout och 0,5 sekund återförsöksintervall:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Det finns två fall beroende på den tid som krävs för att få svar från orchestration-instans:

1. Orchestration-instanser slutföras inom den angivna tidsgränsen (i det här fallet 2 sekunder), svaret är levereras synkront faktiska orchestration instans utdata:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Orchestration-instanser kan inte slutföras inom den angivna tidsgränsen (i det här fallet 2 sekunder), svaret är standard en beskrivs i **HTTP-URL för API-identifiering**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formatet på webhook-URL: er kan variera beroende på vilken version av Azure Functions-värden som du kör. I föregående exempel är för Azure Functions 2.0-värden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder HTTP-APIs exempelvis management](durable-functions-http-api.md)
