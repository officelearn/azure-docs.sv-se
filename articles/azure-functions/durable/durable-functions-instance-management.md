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
ms.openlocfilehash: 1ab2e35c916c6bd6f2d73a328f71710378fac890
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343946"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Hantera instanser i varaktiga funktioner (Azure Functions)

[Varaktiga funktioner](durable-functions-overview.md) orchestration-instanser kan vara igång, avslutas, efterfrågas och skickas meddelanden. Alla instanshantering görs med hjälp av den [orkestreringsklient bindning](durable-functions-bindings.md). Den här artikeln går i detaljerna för varje instans management-åtgärd.

## <a name="starting-instances"></a>Start instanser

[StartNewAsync] (https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) eller `startNew` på den `DurableOrchestrationClient` (JavaScript) startar en ny instans av en orchestrator-funktion. Instanser av den här klassen kan fås med hjälp av den `orchestrationClient` bindning. Internt, den här metoden placerar det i kö ett meddelande till kön kontroll, som utlöser sedan början av en funktion med det angivna namnet som använder den `orchestrationTrigger` utlösa bindning.

Den här asynkrona åtgärden har slutförts när orchestration-process har schemalagts. Orchestration-processen bör starta inom 30 sekunder. Om det tar längre tid, en `TimeoutException` genereras.

> [!WARNING]
> När du utvecklar lokalt i JavaScript, behöver du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t.ex. `localhost:7071` att använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Parametrarna för [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) är följande:

* **Namn på**: Namnet på orchestrator-funktion för att schemalägga.
* **Indata**: JSON-serialiserbara data som ska skickas som indata till orchestrator-funktion.
* **instanceId**: (Valfritt) Unikt ID för instansen. Om inte anges genereras ett slumpmässigt instans-ID.

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

* **Namn på**: Namnet på orchestrator-funktion för att schemalägga.
* **instanceId**: (Valfritt) Unikt ID för instansen. Om inte anges genereras ett slumpmässigt instans-ID.
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

> [!NOTE]
> Använda en slumpmässig identifierare för instans-ID. Detta bidrar till en lika med belastningsutjämning vid skalning orchestrator-funktioner mellan flera virtuella datorer. Rätt tid att använda icke-slumpmässiga instans-ID: N är när det ID: T måste komma från en extern källa eller när du implementerar den [singleton orchestrator](durable-functions-singletons.md) mönster.

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det är också möjligt att starta en förekomst direkt via den [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` kommando. Det tar följande parametrar:

* **`function-name` (krävs)** : Namnet på funktionen för att starta
* **`input` (valfritt)** : Indata till funktionen, antingen i infogade eller via en JSON-fil. Prefix för filer, sökvägen till filen med `@`, till exempel `@path/to/file.json`.
* **`id` (valfritt)** : ID för orchestration-instans. Om det inte anges, skapas en slumpmässig GUID.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

> [!NOTE]
> Core Tools kommandon förutsätter att de utförs från rotkatalogen för en funktionsapp. Om `connection-string-setting` och `task-hub-name` uttryckligen har angett, kommandona kan köras från en katalog. Även om dessa kommandon kan köras utan en funktionen app-värd som kör, kanske inte vissa effekter observeras om inte värden körs. Till exempel den `start-new` kommando kommer sätta ett startmeddelande till mål uppgift hub, men orchestration körs inte faktiskt om det inte finns en funktion app värdprocess som körs som kan bearbeta meddelandet.

Följande kommando skulle starta funktionen med namnet HelloWorld och skicka innehållet i filen ”räknaren-data.json” till den:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Fråga instanser

Den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metoden på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `getStatus` metoden på den `DurableOrchestrationClient` klass (JavaScript) frågar efter statusen för en orkestrering instans.

Det tar en `instanceId` (obligatoriskt), `showHistory` (valfritt), `showHistoryOutput` (valfritt) och `showInput` (valfritt, endast .NET) som parametrar.

* **`showHistory`**: Om inställd `true`, svaret innehåller körningshistorik.
* **`showHistoryOutput`**: Om inställd `true`, körningshistorik innehåller aktivitetsutdata.
* **`showInput`**: Om inställd `false`, svaret innehåller inte indata för funktionen. Standardvärdet är `true`. (.NET)

Metoden returnerar ett JSON-objekt med följande egenskaper:

* **Namn på**: Namnet på orchestrator-funktion.
* **instanceId**: Instans-ID för dirigering (bör vara samma som den `instanceId` indata).
* **CreatedTime**: Tid då starta orchestrator-funktion som körs.
* **LastUpdatedTime**: Tidpunkt då orchestration senaste med kontrollpunkt.
* **Indata**: Indata för funktionen som ett JSON-värde. Det här fältet fylls inte om `showInput` är FALSKT.
* **CustomStatus**: Anpassad orkestreringsstatus i JSON-format.
* **Utdata**: Resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades, innehåller den här egenskapen information om felet. Om orchestrator-funktion har avslutats tas den tillhandahållna orsaken till att den här egenskapen (om sådan finns).
* **runtimeStatus**: En av följande värden:
  * **Väntande**: Instansen har schemalagts men har startats inte som körs.
  * **Kör**: Instansen har startats.
  * **Slutfört**: Instansen har slutförts normalt.
  * **ContinuedAsNew**: Instansen har startats om själva med en lista. Det här är ett tillfälligt tillstånd.
  * **Det gick inte**: Instansen misslyckades med ett fel.
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

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det är också möjligt att hämta status för en orchestration-instans direkt via den [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans
* **`show-input` (valfritt)** : Om inställd `true`, svaret innehåller indata för funktionen. Standardvärdet är `false`.
* **`show-output` (valfritt)** : Om inställd `true`, svaret innehåller resultatet av funktionen. Standardvärdet är `false`.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Följande kommando returnerar status (inklusive indata och utdata) för en instans med ett instans-ID för dirigering av 0ab8c55a66644d68a3a8b220b12d209c. Det förutsätter att den `func` kommandot körs från rotkatalogen för funktionsappen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Den `durable get-history` kommando kan användas för att hämta historiken för en orchestration-instans. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges i host.json via durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Fråga alla instanser

Du kan använda den `GetStatusAsync` (.NET) eller `getStatusAll` (JavaScript)-metod för att fråga status för alla orchestration-instanser. I .NET kan du skicka en `CancellationToken` objekt om du vill avbryta den. Metoden returnerar ett objekt med samma egenskaper som den `GetStatusAsync` metod med parametrar.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
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

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det är också möjligt att frågan instanser direkt via den [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` kommando. Det tar följande parametrar:

* **`top` (valfritt)** : Det här kommandot stöder sidindelning. Den här parametern motsvarar antalet instanser som tas emot per begäran. Standardvärdet är 10.
* **`continuation-token` (valfritt)** : En token för att ange vilka/sidavsnitt instanser ska hämtas. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Fråga instanser med filter

Du kan också använda den `GetStatusAsync` (.NET) eller `getStatusBy` (JavaScript)-metod för att hämta en lista över orchestration-instanser som matchar en uppsättning fördefinierade filter. Möjliga filteralternativ är tiden för skapandet av orchestration och Körningsstatus för orkestrering.

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

### <a name="using-the-functions-core-tools"></a>Med Functions Core Tools

Den `durable get-instances` kommando kan också användas med filter. Förutom det ovan nämnda `top`, `continuation-token`, `connection-string-setting`, och `task-hub-name` parametrar, tre filterparametrar (`created-after`, `created-before`, och `runtime-status`), kan användas.

* **`created-after` (valfritt)** : Hämta instanser som skapats efter det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`created-before` (valfritt)** : Hämta instanser som skapats innan det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`runtime-status` (valfritt)** : Hämta instanser vars status matchar dessa (”körs”, ”slutförd” osv.). Ange status för flera (avgränsade med blanksteg).
* **`top` (valfritt)** : Antal instanser hämtas per begäran. Standardvärdet är 10.
* **`continuation-token` (valfritt)** : En token för att ange vilka/sidavsnitt instanser ska hämtas. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Om inga filter (`created-after`, `created-before`, eller `runtime-status`) har angetts och sedan `top` instanser som hämtas med ingen hänsyn till runtime status eller skapa en gång.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Avslutande instanser

En orchestration-instans som körs avslutas med hjälp av den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `terminate` -metoden för den `DurableOrchestrationClient` klassen () JavaScript). De två parametrarna är en `instanceId` och en `reason` strängen, som skrivs till loggarna och status för instans. En avslutad instans stoppas när den når nästa `await` (.NET) eller `yield` punkt (JavaScript), eller så avslutas omedelbart om det redan är på en `await` (.NET) eller `yield` (JavaScript).

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
> Instans-avslutning för närvarande inte spridas. Aktivitetsfunktioner och underordnade orkestreringar körs klart oavsett om orchestration-instans som kallas dem har avslutats.

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det går också att avsluta en orchestration-instans direkt via den [Kärnverktyg](../functions-run-local.md) `durable terminate` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instansen avslutas
* **`reason` (valfritt)** : Orsak till avslutning
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Följande kommando skulle avsluta en orchestration-instans med ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Skicka händelser till instanser

Händelsemeddelanden kan skickas till kör instanser med hjälp av den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass (.NET) eller `raiseEvent` -metoden för den `DurableOrchestrationClient` klassen () JavaScript). Instanser som kan hantera de här händelserna är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) eller `waitForExternalEvent` (JavaScript).

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

> [!WARNING]
> Om det finns ingen orchestration-instans med det angivna *instans-ID* eller om instansen inte väntar på den angivna *händelsenamn*, händelsemeddelandet ignoreras. Mer information om det här problemet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det är också möjligt att skapa en händelse till en orchestration-instans direkt via den [Kärnverktyg](../functions-run-local.md) `durable raise-event` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans
* **`event-name` (valfritt)** : Namnet på händelsen ska aktiveras. Standardvärdet är `$"Event_{RandomGUID}"`
* **`event-data` (valfritt)** : Data som skickas till orchestration-instans. Detta kan vara sökvägen till en JSON-fil eller data kan anges direkt på kommandoraden
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Vänta på orchestration-slutförande

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API i .NET som kan användas för att hämta synkront faktiska utdata från en orkestrering instans. I JavaScript, den `DurableOrchestrationClient` klassen visar en `waitForCompletionOrCreateCheckStatusResponse` API för samma ändamål. Metoderna som använder ett standardvärde på 10 sekunder tills `timeout` och 1 sekund för `retryInterval` när de har inte angetts.  

Här är ett exempel HTTP-utlösare-funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Funktionen kan anropas med följande rad med 2 sekunder timeout och återförsöksintervall för 0,5 sekund:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Beroende på den tid som krävs för att få ett svar från orchestration-instans, finns det två fall:

* Orchestration-instanser slutförs inom den definierade tidsgränsen (i det här fallet 2 sekunder), svaret är faktiska orchestration instans utdata levereras synkront:

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

* Orchestration-instanser kan inte slutföras inom den definierade tidsgränsen (i det här fallet 2 sekunder), svaret är något som beskrivs i standard **HTTP-URL för API-identifiering**:

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
> Formatet för webhook-URL: er kan variera beroende på vilken version av Azure Functions-värden som du kör. I föregående exempel är för Azure Functions 2.x värden.

## <a name="retrieving-http-management-webhook-urls"></a>Hämtning av HTTP-hantering av Webhook-URL: er

Externa systemen kan kommunicera med varaktiga funktioner via de webhook-URL: er som ingår i Standardsvaret som beskrivs i [HTTP-URL för API-identifiering](durable-functions-http-api.md). Dock webhook-URL: er även kan nås via programmering i orchestration-klienten eller i en aktivitet funktion via den [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)klass (.NET) eller `createHttpManagementPayload` -metoden för den `DurableOrchestrationClient` klass (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) och `createHttpManagementPayload` ha en parameter:

* **instanceId**: Unikt ID för instansen.

Metoderna som returnerar en instans av [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) eller ett objekt (JavaScript) med följande strängegenskaper:

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

## <a name="rewinding-instances-preview"></a>Spola tillbaka instances (förhandsversion)

En misslyckad orchestration-instans kan vara *spolas tillbaka* till en tidigare felfritt tillstånd med hjälp av den [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) eller `rewindAsync` (JavaScript) API. Det fungerar genom att placera orchestration tillbaka till den *kör* tillstånd och köra aktiviteten och/eller suborchestration körning fel som orsakade orchestration-fel.

> [!NOTE]
> Detta API är inte avsedd att vara en ersättning för rätt felhantering och försöka principer. I stället är den avsedd att användas endast i fall där orchestration-instanser misslyckas av oväntat orsaker. Mer information om fel felhantering och principer finns i den [felhantering](durable-functions-error-handling.md) avsnittet.

Ett exempel användningsfall för *spola tillbaka* är ett arbetsflöde som omfattar en serie [mänskliga godkännanden](durable-functions-overview.md#pattern-5-human-interaction). Anta att det finns ett antal Aktivitetsfunktioner som meddelar någon att deras godkännande krävs och vänta ut i realtid svaret. För godkännandet har när alla aktiviteter fått svar eller Tidsgränsen nåddes under en annan aktivitet misslyckas på grund av ett program felkonfiguration, t.ex en ogiltig databasanslutningssträng. Resultatet är ett orchestration-fel i arbetsflödet. Med den `RewindAsync` (.NET) eller `rewindAsync` (JavaScript) API, programadministratör kan åtgärda felet för konfiguration och *spola tillbaka* misslyckade orchestration tillbaka till tillståndet omedelbart före felet. Inget av stegen som mänsklig interaktion behöver godkännas och dirigering kan nu slutföras.

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

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det går också att spola tillbaka en orchestration-instans direkt via den [Kärnverktyg](../functions-run-local.md) `durable rewind` kommando. Det tar följande parametrar:

* **`id` (krävs)** : ID för orchestration-instans
* **`reason` (valfritt)** : Orsaken till spola tillbaka orchestration-instans
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Rensa historiken för instans

> [!NOTE]
> Den `PurgeInstanceHistoryAsync` API är för närvarande endast tillgänglig för C#. Det läggs till JavaScript i en kommande version.

Orchestration-Historik kan rensas med hjälp av [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). Funktionen tar bort alla data som är associerade med en orkestrering - tabellrader i Azure och stora meddelanden BLOB-objekt om de finns. Metoden har två överlagringar. Den första som rensar historik av orchestration-instans-ID:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Det andra exemplet visar en timerutlöst funktion som rensar historiken för alla orchestration-instanser som slutförda efter det angivna tidsintervallet. I det här fallet tas data för alla instanser som slutförts 30 dagar sedan. Den är schemalagd att köras en gång per dag kl. 12:

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
> Den *PurgeInstanceHistory* överlagring accepterar tidsperiod som parameter kommer att bearbeta endast orchestration-instanser i en av Körningsstatus - slutförts, Uppsagd eller misslyckades.

### <a name="using-core-tools"></a>Med hjälp av Core Tools

Det är möjligt att rensa historiken för en orchestration-instans med hjälp av den [Kärnverktyg](../functions-run-local.md) `durable purge-history` kommando. Liknar andra C# exemplet ovan rensar historiken för alla orchestration-instanser som skapats under ett angivet tidsintervall. Instanserna rensas kan filtreras ytterligare efter Körningsstatus. Kommandot har flera parametrar:

* **`created-after` (valfritt)** : Rensa historiken för instanser som skapats efter det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`created-before` (valfritt)** : Rensa historiken för instanser som skapats före det här datum/tid (UTC). ISO 8601 formaterad datum och tid som har accepterat.
* **`runtime-status` (valfritt)** : Rensa historiken för instanser vars status matchar dessa (”körs”, ”slutförd” osv.). Ange status för flera (avgränsade med blanksteg).
* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Följande kommando tar bort historiken för alla misslyckade instanser som skapats innan den 14 November 2018 klockan 7:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Tar bort en uppgift-hubb

Med hjälp av den [Kärnverktyg](../functions-run-local.md) `durable delete-task-hub` kommandot, är det möjligt att ta bort alla storage artefakter som associeras med en viss aktivitet hubb. Detta inkluderar Azure storage-tabeller, köer och blobs. Kommandot har två parametrar:

* **`connection-string-setting` (valfritt)** : Namnet på inställningen som innehåller anslutningssträngen för lagring att använda. Standardvärdet är AzureWebJobsStorage.
* **`task-hub-name` (valfritt)** : Namnet på hubben varaktiga uppgift att använda. Standardvärdet är DurableFunctionsHub. Det kan också anges [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Följande kommando tar bort alla Azure storage-data som hör till 'UserTest' uppgift hubben.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder HTTP-APIs exempelvis management](durable-functions-http-api.md)
