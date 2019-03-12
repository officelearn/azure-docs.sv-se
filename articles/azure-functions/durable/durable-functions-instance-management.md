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
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547324"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Hantera instanser i varaktiga funktioner i Azure

Om du använder den [varaktiga funktioner](durable-functions-overview.md) tillägget för Azure Functions eller vill du starta gjort det, se till att du får bästa användning av den. Du kan optimera dina varaktiga funktioner orchestration-instanser genom att lära dig mer om hur du hanterar dem. Den här artikeln går i detaljerna för varje instans management-åtgärd.

Du kan starta och avsluta instanser, till exempel, och du kan fråga efter instanser, inklusive möjligheten att fråga efter alla instanser och fråga instanser med filter. Dessutom kan du skicka händelser till instanser, vänta på slutförande av orchestration och hämta HTTP management webhook-URL: er. Den här artikeln beskriver andra hanteringsåtgärder, utan också, inklusive spola tillbaka instanser, Rensa historik instans och tar bort en uppgift-hubb.

I varaktiga funktioner har du alternativ för hur du vill implementera var och en av dessa åtgärder. Den här artikeln innehåller exempel som använder den [Azure Functions Core Tools](../functions-run-local.md) för både .NET (C#) och JavaScript.

## <a name="start-instances"></a>Start instanser

Det är viktigt för att kunna starta en förekomst av dirigering. Detta är vanligt när du använder en bindning för varaktiga funktioner i en annan funktion utlösaren.

Den [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) eller `startNew` på den `DurableOrchestrationClient` (JavaScript) startar en ny instans. Du skaffar instanser av den här klassen med hjälp av den `orchestrationClient` bindning. Internt, den här metoden placerar det i kö ett meddelande till kön kontroll, som utlöser sedan början av en funktion med det angivna namnet som använder den `orchestrationTrigger` utlösa bindning.

Den här asynkrona åtgärden har slutförts när orchestration-process har schemalagts. Orchestration-processen bör starta inom 30 sekunder. Om det tar längre tid, visas en `TimeoutException`.

> [!WARNING]
> När du utvecklar lokalt i JavaScript, ange miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>` (till exempel `localhost:7071`) att använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametrarna för [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) är följande:

* **Namn**: Namnet på orchestrator-funktion för att schemalägga.
* **Indata**: JSON-serialiserbara data som ska skickas som indata till orchestrator-funktion.
* **instanceId**: (Valfritt) Unikt ID för instansen. Om du inte anger den här parametern använder metoden ett slumpmässigt-ID.

Här är ett enkelt C#-exempel:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

Parametrarna för `startNew` är följande:

* **Namn**: Namnet på orchestrator-funktion för att schemalägga.
* **instanceId**: (Valfritt) Unikt ID för instansen. Om du inte anger den här parametern använder metoden ett slumpmässigt-ID.
* **Indata**: (Valfritt) JSON-serialiserbara data som ska skickas som indata till orchestrator-funktion.

Här är ett enkelt JavaScript-exempel:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Använda en slumpmässig identifierare för instans-ID. Detta säkerställer en lika belastningsfördelning när du skalar orchestrator-funktioner mellan flera virtuella datorer. I rätt tid för att använda icke-slumpmässiga instans-ID: N är när det ID: T måste komma från en extern källa, eller när du implementerar den [singleton orchestrator](durable-functions-singletons.md) mönster.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också starta en förekomst direkt med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` kommando. Det tar följande parametrar:

* **`function-name` (krävs)** : Namnet på funktionen för att starta.
* **`input` (valfritt)** : Indata till funktionen, antingen direkt eller via en JSON-fil. Filer, lägga till ett prefix i sökvägen till filen med `@`, till exempel `@path/to/file.json`.
* **`id` (valfritt)** : ID för orchestration-instans. Om du inte anger den här parametern används en slumpmässig GUID.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är DurableFunctionsHub. Du kan också ange detta [host.json](durable-functions-bindings.md#host-json) med hjälp av durableTask:HubName.

> [!NOTE]
> Core Tools kommandon förutsätter att du kör dem från rotkatalogen för en funktionsapp. Om du uttryckligen anger den `connection-string-setting` och `task-hub-name` parametrar, kan du köra kommandon från en katalog. Även om du kan köra dessa kommandon utan en funktionen app-värd som kör, kanske du upptäcker att du inte kan se vissa effekter om inte värden körs. Till exempel den `start-new` kommandot placerar det i kö startmeddelandet till mål uppgift navet, men orchestration faktiskt inte fungerar som om det inte finns en funktion app värd-process som körs som kan bearbeta meddelandet.

Följande kommando startar funktionen med namnet HelloWorld och skickar innehållet i filen `counter-data.json` till den:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Fråga instanser

Som en del av ditt arbete för att hantera dina orkestreringar, behöver du troligen samla in information om status för en orchestration-instans (till exempel om den har slutförts normalt eller misslyckade).

Den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `getStatus` metoden på den `DurableOrchestrationClient` klass (JavaScript) frågar efter statusen för en orkestrering instans.

Det tar en `instanceId` (obligatoriskt), `showHistory` (valfritt), `showHistoryOutput` (valfritt) och `showInput` (valfritt, endast .NET) som parametrar.

* **`showHistory`**: Om inställd `true`, svaret innehåller körningshistorik.
* **`showHistoryOutput`**: Om inställd `true`, körningshistorik innehåller aktivitetsutdata.
* **`showInput`**: Om inställd `false`, svaret inte innehåller indata för funktionen. Standardvärdet är `true`. (.NET)

Metoden returnerar ett JSON-objekt med följande egenskaper:

* **Namn**: Namnet på orchestrator-funktion.
* **instanceId**: Instans-ID för dirigering (bör vara samma som den `instanceId` indata).
* **CreatedTime**: Tid då starta orchestrator-funktion som körs.
* **LastUpdatedTime**: Tidpunkt då orchestration senaste med kontrollpunkt.
* **Indata**: Indata för funktionen som ett JSON-värde. Det här fältet är inte fyllts i om `showInput` är FALSKT.
* **CustomStatus**: Anpassad orkestreringsstatus i JSON-format.
* **Utdata**: Resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades, innehåller den här egenskapen information om felet. Om orchestrator-funktion har avslutats, inkluderar den här egenskapen orsaken till att (i förekommande fall).
* **RuntimeStatus**: En av följande värden:
  * **Väntande**: Instansen har schemalagts men har startats inte som körs.
  * **Körning**: Instansen har startats.
  * **Slutfört**: Instansen har slutförts normalt.
  * **ContinuedAsNew**: Instansen har startats om själva med en lista. Det här är ett tillfälligt tillstånd.
  * **Misslyckades**: Instansen misslyckades med ett fel.
  * **Avslutas**: Instansen stoppades tvärt.
* **Historik**: Körningstiden för dirigering. Det här fältet fylls bara om `showHistory` är inställd på `true`.

Den här metoden returnerar `null` om instansen finns inte eller inte har startats som körs.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Det är också möjligt att hämta status för en orchestration-instans direkt, med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans.
* **`show-input` (valfritt)** : Om inställd `true`, svaret innehåller indata för funktionen. Standardvärdet är `false`.
* **`show-output` (valfritt)** : Om inställd `true`, svaret innehåller resultatet av funktionen. Standardvärdet är `false`.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando hämtar status (inklusive indata och utdata) för en instans med ett instans-ID för dirigering av 0ab8c55a66644d68a3a8b220b12d209c. Det förutsätter att du använder den `func` från rotkatalogen för funktionsappen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Du kan använda den `durable get-history` kommando för att hämta historiken för en orchestration-instans. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges i host.json, med hjälp av durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Fråga efter alla instanser

I stället för att fråga en instans i din orchestration samtidigt, kan det vara mer effektivt att fråga efter alla på samma gång.

Du kan använda den `GetStatusAsync` (.NET) eller `getStatusAll` (JavaScript)-metod för att fråga status för alla orchestration-instanser. I .NET, kan du skicka en `CancellationToken` objekt om du vill avbryta den. Metoden returnerar ett objekt med samma egenskaper som den `GetStatusAsync` metod med parametrar.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Det är också möjligt att frågan instanser direkt, med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` kommando. Det tar följande parametrar:

* **`top` (valfritt)** : Det här kommandot stöder sidindelning. Den här parametern motsvarar antalet instanser som tas emot per begäran. Standardvärdet är 10.
* **`continuation-token` (valfritt)** : En token för att ange vilken sida eller delen av instanser för att hämta. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Fråga instanser med filter

Vad händer om du inte verkligen behöver all information som ger en standardinstans fråga? Till exempel vad händer om du bara söker orchestration-tiden för skapandet eller Körningsstatus orchestration? Du kan begränsa din fråga genom att använda filter.

Använd den `GetStatusAsync` (.NET) eller `getStatusBy` (JavaScript)-metod för att hämta en lista över orchestration-instanser som matchar en uppsättning fördefinierade filter.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

I Azure Functions Core Tools kan du också använda den `durable get-instances` med filter. Förutom det ovan nämnda `top`, `continuation-token`, `connection-string-setting`, och `task-hub-name` parametrar, som du kan använda tre filterparametrar (`created-after`, `created-before`, och `runtime-status`).

* **`created-after` (valfritt)** : Hämta instanser som skapats efter det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`created-before` (valfritt)** : Hämta instanser som skapats innan det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`runtime-status` (valfritt)** : Hämta instanser med en viss status (till exempel körs eller har slutförts). Ange status för flera (avgränsade med blanksteg).
* **`top` (valfritt)** : Antal instanser hämtas per begäran. Standardvärdet är 10.
* **`continuation-token` (valfritt)** : En token för att ange vilken sida eller delen av instanser för att hämta. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Om du inte anger några filter (`created-after`, `created-before`, eller `runtime-status`), kommandot hämtar bara `top` instanser och ingen hänsyn till runtime status eller skapa en gång.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Avsluta instanser

Om du har en orchestration-instans som tar för lång tid eller behöver du bara stoppa den innan den slutförs av någon anledning, har du möjlighet att avsluta den.

Du kan använda den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `terminate` -metoden för den `DurableOrchestrationClient` klass (JavaScript). De två parametrarna är en `instanceId` och en `reason` strängen, som skrivs till loggarna och status för instansen. En avslutad instans slutar att köras när den når nästa `await` (.NET) eller `yield` punkt (JavaScript), eller så avslutas omedelbart om det redan finns en `await` eller `yield`.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Instans-avslutning spridas för närvarande inte. Aktivitetsfunktioner och underordnade orkestreringar kör för att åtgärden har slutförts, oavsett om du har avslutats orchestration-instans som kallas dem.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan även avsluta en orchestration-instans direkt, med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instansen avslutas.
* **`reason` (valfritt)** : Orsak till uppsägning.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando avslutar en orchestration-instans med ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Skicka händelser till instanser

I vissa situationer kan är det viktigt för orchestrator-funktioner för att kunna vänta och lyssna efter externa händelser. Detta inkluderar [övervaka functions](durable-functions-concepts.md#monitoring) och funktioner som väntar på [mänsklig interaktion](durable-functions-concepts.md#human).

Skicka händelsemeddelanden till instanser körs med hjälp av den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `raiseEvent` -metoden för den `DurableOrchestrationClient` klassen () JavaScript). Instanser som kan hantera de här händelserna är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) eller `waitForExternalEvent` (JavaScript).

Parametrarna för [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) och `raiseEvent` (JavaScript) är följande:

* **instanceId**: Unikt ID för instansen.
* **EventName**: Namnet på händelsen att skicka.
* **EventData**: En JSON-serialiserbara för att skicka till instansen.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Om det finns ingen orchestration-instans med angivna instans-ID, eller om instansen inte väntar på det angivna händelsenamnet, ignoreras händelsemeddelandet. Mer information om det här problemet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också medföra en händelse till en orchestration-instans direkt, med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans.
* **`event-name` (valfritt)** : Namnet på händelsen ska aktiveras. Standardvärdet är `$"Event_{RandomGUID}"`.
* **`event-data` (valfritt)** : Data som skickas till orchestration-instans. Detta kan vara sökvägen till en JSON-fil eller du kan ange data direkt på kommandoraden.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Vänta på orchestration-slutförande

I tidskrävande orkestreringar, kan du vänta och hämta resultatet av en orkestrering. I dessa fall är det också användbart för att kunna definiera en timeout-period på dirigering. Om tidsgränsen överskrids ska tillståndet för dirigering returneras i stället för resultaten.

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API i .NET. Du kan använda detta API för att få den faktiska utdatan från en instans av orchestration synkront. I JavaScript, den `DurableOrchestrationClient` klassen visar en `waitForCompletionOrCreateCheckStatusResponse` API för samma ändamål. När de inte har angetts i metoder används standardvärdet 10 sekunder tills `timeout`, och 1 sekund för `retryInterval`.  

Här är ett exempel HTTP-utlösare-funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Anropa funktionen med följande rad. Använd 2 sekunder för timeout och 0,5 sekunder för återförsöksintervallet:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Beroende på den tid som krävs för att få ett svar från orchestration-instans, finns det två fall:

* Orchestration-instanser slutförs inom den definierade tidsgränsen (i det här fallet 2 sekunder) och svaret är faktiska orchestration instans utdata levereras synkront:

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

* Orchestration-instanser kan inte slutföras inom den definierade tidsgränsen och svaret är standard en beskrivs i [HTTP-URL för API-identifiering](durable-functions-http-api.md):

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
> Formatet för webhook-URL: er kan variera, beroende på vilken version av Azure Functions-värden som du kör. I föregående exempel är för Azure Functions 2.x värden.

## <a name="retrieve-http-management-webhook-urls"></a>Hämta HTTP management webhook-URL: er

Du kan använda ett externt system för att övervaka eller generera händelser till en orkestrering. Externa systemen kan kommunicera med varaktiga funktioner via de webhook-URL: er som ingår i Standardsvaret som beskrivs i [HTTP-URL för API-identifiering](durable-functions-http-api.md). Dock kan webhook-URL: er också nås via programmering i orchestration-klienten eller i en aktivitet-funktion. Gör detta med hjälp av den [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `createHttpManagementPayload` -metoden för den `DurableOrchestrationClient` klass (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) och `createHttpManagementPayload` ha en parameter:

* **instanceId**: Unikt ID för instansen.

Metoderna som returnerar en instans av [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) eller ett objekt (JavaScript), med följande strängegenskaper:

* **ID**: Instans-ID för dirigering (bör vara samma som den `InstanceId` indata).
* **StatusQueryGetUri**: Status för Webbadressen till orchestration-instans.
* **SendEventPostUri**: ”Rera händelse” Webbadressen till orchestration-instans.
* **TerminatePostUri**: ”Avsluta” Webbadressen till orchestration-instans.
* **RewindPostUri**: ”Tillbakaspolning” Webbadressen till orchestration-instans.

Aktivitetsfunktioner kan skicka en instans av de här objekten till externa system för att övervaka eller generera händelser till en orkestrering:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Tillbakaspolning instances (förhandsversion)

Om du har ett fel för dirigering av en oväntad orsak, kan du *spola tillbaka* instansen till en tidigare felfritt tillstånd genom att använda ett API som byggts för detta ändamål.

> [!NOTE]
> Detta API är inte avsedd att vara en ersättning för rätt felhantering och försöka principer. I stället är den avsedd att användas endast i fall där orchestration-instanser misslyckas av oväntat orsaker. Mer information om fel felhantering och principer finns i den [felhantering](durable-functions-error-handling.md) avsnittet.

Använd den [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) eller `rewindAsync` (JavaScript) API för att placera orchestration tillbaka till den *kör* tillstånd. Kör aktivitet eller suborchestration körning fel som orsakade orchestration-fel.

Exempel: Anta att du har ett arbetsflöde som omfattar en serie [mänskliga godkännanden](durable-functions-concepts.md#human). Anta att det finns ett antal Aktivitetsfunktioner som meddelar någon som godkännande krävs och vänta ut i realtid svaret. När alla aktiviteter för godkännande har fått svar eller timeout, anta att en annan aktivitet misslyckas på grund av ett program felkonfiguration, t.ex en ogiltig databasanslutningssträng. Resultatet är ett orchestration-fel i arbetsflödet. Med den `RewindAsync` (.NET) eller `rewindAsync` (JavaScript) API, ett program-administratör kan åtgärda felet för konfiguration och spola tillbaka misslyckade orchestration tillbaka till tillståndet omedelbart före felet. Inget av stegen som mänsklig interaktion behöver godkännas och dirigering kan nu slutföras.

> [!NOTE]
> Den *spola tillbaka* funktionen stöder inte spola tillbaka orchestration instanser som använder varaktiga timers.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också spola tillbaka en orchestration-instans direkt med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans.
* **`reason` (valfritt)** : Orsak för spola tillbaka orchestration-instans.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Rensa historiken för instans

Du kan rensa historiken för instansen för att ta bort alla data som är associerade med en orkestrering. Exempelvis kan du ta bort tabellrader i Azure och stora meddelanden blobbar, om de finns. Du gör detta genom att använda den [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> Den `PurgeInstanceHistoryAsync` API är för närvarande endast tillgänglig för C#.

 Metoden har två överlagringar. Den första som rensar Historik efter ID för orchestration-instans:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Det andra exemplet visar en timerutlöst funktion som rensar historiken för alla orchestration-instanser som slutförda efter det angivna tidsintervallet. I det här fallet tas bort data för alla instanser som slutförts 30 dagar sedan. Den är schemalagd för att köras en gång per dag kl. 12:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> För tid-utlöst funktion hämtningsprocessen ska lyckas måste Körningsstatus måste vara **slutförd**, **Uppsagd**, eller **misslyckades**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan rensa historiken för en orchestration-instans med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` kommando. Liknar andra C# exemplet i föregående avsnitt, rensar historiken för alla orchestration-instanser som skapats under ett angivet tidsintervall. Du kan filtrera borttagna instanser av Körningsstatus ytterligare. Kommandot har flera parametrar:

* **`created-after` (valfritt)** : Rensa historiken för instanser som skapats efter det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`created-before` (valfritt)** : Rensa historiken för instanser som skapats före det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`runtime-status` (valfritt)** : Rensa historiken för instanser med en viss status (till exempel körs eller har slutförts). Ange status för flera (avgränsade med blanksteg).
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando tar bort historiken för alla misslyckade instanser som skapats innan den 14 November 2018 klockan 7:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Ta bort en uppgift-hubb

Med hjälp av den [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` kommandot kan du ta bort alla storage artefakter som associeras med en viss aktivitet hubb. Detta inkluderar Azure storage-tabeller, köer och blobs. Kommandot har två parametrar:

* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är `AzureWebJobsStorage`.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga funktioner uppgift att använda. Standardvärdet är `DurableFunctionsHub`. Det kan också anges [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando tar bort alla Azure storage-data som är associerade med den `UserTest` uppgift hub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder HTTP-APIs exempelvis management](durable-functions-http-api.md)
