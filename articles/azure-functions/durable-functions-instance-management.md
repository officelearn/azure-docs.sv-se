---
title: Hantera instanser i varaktiga funktioner – Azure
description: Lär dig hur du hanterar instanser i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: 70ea13c1badf79c86bed53a34d9036706dbbac6a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378164"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Hantera instanser i varaktiga funktioner (Azure Functions)

[Varaktiga funktioner](durable-functions-overview.md) orchestration-instanser kan vara igång, avslutas, efterfrågas och skickas meddelanden. Alla instanshantering görs med hjälp av den [orkestreringsklient bindning](durable-functions-bindings.md). Den här artikeln går i detaljerna för varje instans management-åtgärd.

## <a name="starting-instances"></a>Start instanser

Den [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) startar en ny instans av en orchestrator-funktion. Instanser av den här klassen kan fås med hjälp av den `orchestrationClient` bindning. Internt, den här metoden placerar det i kö ett meddelande till kön kontroll, som utlöser sedan början av en funktion med det angivna namnet som använder den `orchestrationTrigger` utlösa bindning. 

Uppgiften slutförs när orchestration-processen startas. Orchestration-processen bör starta inom 30 sekunder. Om det tar längre tid, en `TimeoutException` genereras. 

Parametrarna för [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) är följande:

* **Namn på**: namnet på orchestrator-funktion för att schemalägga.
* **Inkommande**: alla JSON-serialiserbara data som ska skickas som indata till orchestrator-funktion.
* **InstanceId**: (valfritt) det unika ID: T för instansen. Om inte anges genereras ett slumpmässigt instans-ID.

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

För icke-.NET-språk, funktionen utdatabindning kan användas för att starta nya instanser samt. I det här fallet kan JSON-serialiserbara objekt som har de ovanstående tre parametrarna som fält användas. Anta exempelvis att följande JavaScript-funktion:

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
> Vi rekommenderar att du använder en slumpmässig identifierare för instans-ID. Detta bidrar till en lika med belastningsutjämning vid skalning orchestrator-funktioner mellan flera virtuella datorer. Rätt tid att använda icke-slumpmässiga instans-ID: N är när det ID: T måste komma från en extern källa eller när du implementerar den [singleton orchestrator](durable-functions-singletons.md) mönster.

## <a name="querying-instances"></a>Fråga instanser

Den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass frågar efter statusen för en orchestration-instans. Det tar en `instanceId` (obligatoriskt), `showHistory` (valfritt) och `showHistoryOutput` (valfritt) som parametrar. Om `showHistory` är inställd på `true`, svaret innehåller körningshistorik. Om `showHistoryOutput` är inställd på `true` , körningshistorik innehåller aktivitetsutdata. Metoden returnerar ett objekt med följande egenskaper:

* **Namn på**: namnet på orchestrator-funktion.
* **InstanceId**: instans-ID för dirigering (bör vara samma som den `instanceId` indata).
* **CreatedTime**: tidpunkten då startades orchestrator-funktion som körs.
* **LastUpdatedTime**: tidpunkt då orchestration senaste med kontrollpunkt.
* **Inkommande**: indata för funktionen som ett JSON-värde.
* **CustomStatus**: anpassad orkestreringsstatus i JSON-format. 
* **Utdata**: resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades, innehåller den här egenskapen information om felet. Om orchestrator-funktion har avslutats tas den tillhandahållna orsaken till att den här egenskapen (om sådan finns).
* **RuntimeStatus**: något av följande värden:
    * **Väntande**: instansen har schemalagts men inte har startats som körs.
    * **Kör**: instansen har startats.
    * **Slutfört**: instansen har slutförts normalt.
    * **ContinuedAsNew**: instansen har startats om själva med en lista. Det här är ett tillfälligt tillstånd.
    * **Det gick inte**: instansen misslyckades med ett fel.
    * **Avslutas**: instansen avbröts tvärt.
* **Historik**: körningshistorik för dirigering. Det här fältet fylls bara om `showHistory` är inställd på `true`.
    
Den här metoden returnerar `null` om instansen finns inte eller inte har startats som körs.

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
## <a name="querying-all-instances"></a>Fråga alla instanser

Du kan använda den `GetStatusAsync` metod för att fråga status för alla orchestration-instanser. Det tar inga parametrar eller du kan skicka en `CancellationToken` objekt om du vill avbryta den. Metoden returnerar ett objekt med samma egenskaper som den `GetStatusAsync` metoden med parametrar, förutom att den inte returnerar historik. 

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

En orchestration-instans som körs avslutas med hjälp av den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. De två parametrarna är en `instanceId` och en `reason` strängen, som skrivs till loggarna och status för instans. En avslutad instans stoppas när den når nästa `await` punkt, eller så avslutas omedelbart om det redan är på en `await`. 

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
> Instans-avslutning för närvarande inte spridas. Aktivitetsfunktioner och underordnade orkestreringar körs klart oavsett om orchestration-instans som kallas dem har avslutats.

## <a name="sending-events-to-instances"></a>Skicka händelser till instanser

Händelsemeddelanden kan skickas till kör instanser med hjälp av den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. Instanser som kan hantera de här händelserna är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Parametrarna för [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) är följande:

* **InstanceId**: unikt ID för instansen.
* **EventName**: namnet på händelsen att skicka.
* **EventData**: en JSON-serialiserbara för att skicka till instansen.

```csharp
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
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamn*, händelsemeddelandet ignoreras. Mer information om det här problemet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Vänta på orchestration-slutförande

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API som kan användas för att hämta synkront faktiska utdata från en orchestration-instans. Metoden använder standardvärdet 10 sekunder tills `timeout` och 1 sekund för `retryInterval` när de har inte angetts.  

Här är ett exempel HTTP-utlösare-funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Funktionen kan anropas med följande rad med 2 sekunder timeout och återförsöksintervall för 0,5 sekund:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Det finns två fall beroende på den tid som krävs för att få ett svar från orchestration-instans:

1. Orchestration-instanser slutförs inom den definierade tidsgränsen (i det här fallet 2 sekunder), svaret är faktiska orchestration instans utdata levereras synkront:

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

2. Orchestration-instanser kan inte slutföras inom den definierade tidsgränsen (i det här fallet 2 sekunder), svaret är något som beskrivs i standard **HTTP-URL för API-identifiering**:

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
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formatet för webhook-URL: er kan variera beroende på vilken version av Azure Functions-värden som du kör. I föregående exempel är för Azure Functions 2.0-värden.

## <a name="retrieving-http-management-webhook-urls"></a>Hämtning av HTTP-hantering av Webhook-URL: er

Externa systemen kan kommunicera med varaktiga funktioner via de webhook-URL: er som ingår i Standardsvaret som beskrivs i [HTTP-URL för API-identifiering](durable-functions-http-api.md). Dock webhook-URL: er även kan nås via programmering i orchestration-klienten eller i en aktivitet funktion via den [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)klass. 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) har en parameter:

* **InstanceId**: unikt ID för instansen.

Metoden returnerar en instans av den [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) med följande strängegenskaper:

* **ID**: instans-ID för dirigering (bör vara samma som den `InstanceId` indata).
* **StatusQueryGetUri**: status-URL: en för orchestration-instans.
* **SendEventPostUri**: ”rera händelse” Webbadressen till orchestration-instans.
* **TerminatePostUri**: ”avsluta” Webbadressen till orchestration-instans.
* **RewindPostUri**: ”tillbakaspolning” Webbadressen till orchestration-instans.

Aktivitetsfunktioner kan skicka en instans av [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) till externa system för att övervaka eller generera händelser till en orkestrering:

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="rewinding-instances-preview"></a>Spola tillbaka instances (förhandsversion)

En misslyckad orchestration-instans kan vara *spolas tillbaka* till en tidigare felfritt tillstånd med hjälp av den [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) API. Det fungerar genom att placera orchestration tillbaka till den *kör* tillstånd och köra aktiviteten och/eller underordnade orchestration körning fel som orsakade orchestration-fel.

> [!NOTE]
> Detta API är inte avsedd att vara en ersättning för rätt felhantering och försöka principer. I stället är den avsedd att användas endast i fall där orchestration-instanser misslyckas av oväntat orsaker. Mer information om fel felhantering och principer finns i den [felhantering](durable-functions-error-handling.md) avsnittet.

Ett exempel användningsfall för *spola tillbaka* är ett arbetsflöde som omfattar en serie [mänskliga godkännanden](durable-functions-overview.md#pattern-5-human-interaction). Anta att det finns ett antal Aktivitetsfunktioner som meddelar någon att deras godkännande krävs och vänta ut i realtid svaret. För godkännandet har när alla aktiviteter fått svar eller Tidsgränsen nåddes under en annan aktivitet misslyckas på grund av ett konfigurationsfel för programmet (t.ex. en ogiltig databasanslutningssträng). Resultatet är ett orchestration-fel i arbetsflödet. Med den `RewindAsync` API, programadministratör kan åtgärda felet för konfiguration och *spola tillbaka* misslyckade orchestration tillbaka till tillståndet omedelbart före felet. Ingen av mänsklig interaktion stegen måste vara godkända igen och dirigering kan nu slutföras.

> [!NOTE]
> Den *spola tillbaka* funktionen stöder inte spola tillbaka orchestration instanser som använder varaktiga timers.

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder HTTP-APIs exempelvis management](durable-functions-http-api.md)
