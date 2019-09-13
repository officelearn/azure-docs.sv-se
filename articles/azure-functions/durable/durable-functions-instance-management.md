---
title: Hantera instanser i Durable Functions – Azure
description: Lär dig hur du hanterar instanser i Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0df6f5f9728a8e48a3257e56ddf8ad23906dc92c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933323"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Hantera instanser i Durable Functions i Azure

Om du använder [Durable Functions](durable-functions-overview.md) -tillägget för Azure Functions eller om du vill börja göra det kontrollerar du att du får den bästa användningen av det. Du kan optimera dina Durable Functions Orchestration-instanser genom att lära dig mer om hur du hanterar dem. Den här artikeln går till information om varje instans hanterings åtgärd.

Du kan starta och avsluta instanser, till exempel och du kan fråga instanser, inklusive möjligheten att fråga alla instanser och fråga efter instanser med filter. Dessutom kan du skicka händelser till instanser, vänta på att dirigeringen ska slutföras och hämta URL: er för HTTP Management webhook. I den här artikeln beskrivs även andra hanterings åtgärder, inklusive omspolning av instanser, rensning av instans historik och borttagning av aktivitets nav.

I Durable Functions har du alternativ för hur du vill implementera var och en av dessa hanterings åtgärder. Den här artikeln innehåller exempel som använder [Azure Functions Core tools](../functions-run-local.md) för både .net (C#) och Java Script.

## <a name="start-instances"></a>Start instanser

Det är viktigt att kunna starta en instans av Orchestration. Detta görs vanligt vis när du använder en Durable Functions-bindning i en annan funktions utlösare.

Metoden [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) på [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) `startNew` `DurableOrchestrationClient` eller (Java Script) startar en ny instans. Du hämtar instanser av den här klassen med hjälp `orchestrationClient` av bindningen. Internt kommer den här metoden att köa ett meddelande i kontroll kön, som sedan utlöser början av en funktion med det angivna namnet som använder `orchestrationTrigger` utlösarens bindning.

Den här asynkrona åtgärden slutförs när Orchestration-processen har schemalagts. Orchestration-processen bör inledas inom 30 sekunder. Om det tar längre tid visas en `TimeoutException`.

### <a name="net"></a>.NET

Parametrarna för [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) är följande:

* **Namn på**: Namnet på Orchestrator-funktionen som ska schemaläggas.
* **Inmatade**: Alla JSON-serialiserbara data som ska skickas som indata till Orchestrator-funktionen.
* **InstanceID**: Valfritt Instansens unika ID. Om du inte anger den här parametern använder metoden ett slumpmässigt ID.

Här följer några exempel:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

Parametrarna för `startNew` är följande:

* **Namn på**: Namnet på Orchestrator-funktionen som ska schemaläggas.
* **InstanceID**: Valfritt Instansens unika ID. Om du inte anger den här parametern använder metoden ett slumpmässigt ID.
* **Inmatade**: Valfritt Alla JSON-serialiserbara data som ska skickas som indata till Orchestrator-funktionen.

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
> Använd en slumpmässig identifierare för instans-ID: t. Detta säkerställer en likvärdig belastnings distribution när du skalar Orchestrator-funktioner över flera virtuella datorer. Den tid det tar att använda icke-slumpmässiga instans-ID: n är när ID: t måste komma från en extern källa eller när du implementerar [singleton Orchestrator](durable-functions-singletons.md) -mönstret.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också starta en instans direkt genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable start-new` . Det tar följande parametrar:

* (krävs): **`function-name`** Namnet på den funktion som ska startas.
* (valfritt): **`input`** Indata till funktionen, antingen i rad eller via en JSON-fil. För filer lägger du till ett prefix till sökvägen till filen med `@`, `@path/to/file.json`till exempel.
* (valfritt): **`id`** ID för Orchestration-instansen. Om du inte anger den här parametern använder kommandot ett slumpmässigt GUID.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är AzureWebJobsStorage.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är DurableFunctionsHub. Du kan också ange detta i [Host. JSON](durable-functions-bindings.md#host-json) med hjälp av DurableTask: HubName.

> [!NOTE]
> Kommandona för Core tools förutsätter att du kör dem från rot katalogen i en Function-app. Om du uttryckligen anger `connection-string-setting` parametrarna och `task-hub-name` kan du köra kommandona från vilken katalog som helst. Även om du kan köra dessa kommandon utan att köra en Function-värd som kör, kan du upptäcka att du inte kan observera vissa effekter om inte värden körs. `start-new` Kommandot kommer till exempel att köa ett Start meddelande i hubben för mål, men dirigeringen körs inte om det inte finns någon funktion för att köra en Function-värd process som kan bearbeta meddelandet.

Följande kommando startar funktionen HelloWorld och skickar innehållet i filen `counter-data.json` till den:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Fråga efter instanser

Som en del av din ansträngning för att hantera dina dirigeringar behöver du förmodligen samla in information om statusen för en Dirigerings instans (till exempel om den har slutförts eller inte).

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) -metoden för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen (.net `getStatus` ) eller metoden i `DurableOrchestrationClient` klassen (Java Script) frågar efter status för en Dirigerings instans.

Det tar `instanceId` (obligatoriskt), `showHistory` (valfritt) `showHistoryOutput` , (valfritt) och `showInput` (valfritt, endast .net) som parametrar.

* **`showHistory`** : Om värdet `true`är, innehåller svaret körnings historiken.
* **`showHistoryOutput`** : Om detta är `true`inställt på, innehåller körnings historiken aktivitets utdata.
* **`showInput`** : Om det är `false`inställt på, innehåller svaret inte indatamängden för funktionen. Standardvärdet är `true`. (Endast .NET)

Metoden returnerar ett JSON-objekt med följande egenskaper:

* **Namn på**: Namnet på Orchestrator-funktionen.
* **InstanceID**: Instans-ID: t för dirigeringen (ska vara detsamma som `instanceId` indatatypen).
* **CreatedTime**: Tiden då Orchestrator-funktionen började köras.
* **LastUpdatedTime**: Tiden då dirigeringen senast pågick.
* **Inmatade**: Indatamängden för funktionen som ett JSON-värde. Det här fältet är inte ifyllt om `showInput` är falskt.
* **CustomStatus**: Anpassad Dirigerings status i JSON-format.
* **Utdata**: Resultatet av funktionen som ett JSON-värde (om funktionen har slutförts). Om Orchestrator-funktionen misslyckades innehåller den här egenskapen fel information. Om Orchestrator-funktionen avbröts innehåller den här egenskapen orsaken till uppsägningen (om det finns några).
* **RuntimeStatus**: Ett av följande värden:
  * **Väntar**: Instansen har schemalagts men har ännu inte börjat köras.
  * **Körning**: Instansen har börjat köras.
  * **Slutfört**: Instansen är normalt slutförd.
  * **ContinuedAsNew**: Instansen har startats om med en ny historik. Detta är ett tillfälligt tillstånd.
  * **Misslyckades**: Instansen misslyckades med ett fel.
  * **Avslutades**: Instansen avbröts plötsligt.
* **Historik**: Körnings historiken för dirigeringen. Det här fältet fylls bara i `showHistory` om är inställt på. `true`

Den här metoden `null` returnerar om instansen antingen inte finns eller inte har startat ännu.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Det är också möjligt att hämta statusen för en Dirigerings instans direkt genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable get-runtime-status` . Det tar följande parametrar:

* (krävs): **`id`** ID för Orchestration-instansen.
* (valfritt): **`show-input`** Om värdet `true`är, innehåller svaret indatatypen för funktionen. Standardvärdet är `false`.
* (valfritt): **`show-output`** Om värdet `true`är, innehåller svaret resultatet av funktionen. Standardvärdet är `false`.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

Följande kommando hämtar status (inklusive indata och utdata) för en instans med ett Dirigerings instans-ID för 0ab8c55a66644d68a3a8b220b12d209c. Det förutsätter att du kör `func` kommandot från rot katalogen i Function-appen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Du kan använda `durable get-history` kommandot för att hämta historiken för en Dirigerings instans. Det tar följande parametrar:

* (krävs): **`id`** ID för Orchestration-instansen.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i Host. JSON, genom att använda durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Fråga alla instanser

I stället för att skicka frågor till en instans i din organisation i taget, kan det vara mer effektivt att fråga alla dem samtidigt.

Du kan använda `GetStatusAsync` metoden (.net) eller `getStatusAll` (Java Script) för att fråga efter status för alla Dirigerings instanser. I .net kan du skicka ett `CancellationToken` objekt om du vill avbryta det. Metoden returnerar objekt med samma egenskaper som `GetStatusAsync` metoden med parametrar.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

Det är också möjligt att fråga instanser direkt, genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable get-instances` . Det tar följande parametrar:

* (valfritt): **`top`** Det här kommandot stöder sid indelning. Den här parametern motsvarar antalet instanser som hämtas per begäran. Standardvärdet är 10.
* (valfritt): **`continuation-token`** En token för att ange vilken sida eller vilket avsnitt av instanser som ska hämtas. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Fråga efter instanser med filter

Vad händer om du verkligen behöver ha all information som en standard instans fråga kan ge? Vad händer till exempel om du bara söker efter skapande tid för Orchestration eller om Dirigerings körnings status? Du kan begränsa frågan genom att använda filter.

`getStatusBy` Använd metoden `GetStatusAsync` (.net) eller (Java Script) för att hämta en lista över Dirigerings instanser som matchar en uppsättning fördefinierade filter.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

I Azure Functions Core Tools kan du också använda `durable get-instances` kommandot med filter. Förutom `top`de ovannämnda `task-hub-name` `created-after` `runtime-status` `created-before`parametrarna,, och kan du använda tre filter parametrar (,, och). `continuation-token` `connection-string-setting`

* (valfritt): **`created-after`** Hämta instanserna som skapats efter detta datum/tid (UTC). ISO 8601 formaterade datetimes har godkänts.
* (valfritt): **`created-before`** Hämta instanserna som skapats före denna datum/tid (UTC). ISO 8601 formaterade datetimes har godkänts.
* (valfritt): **`runtime-status`** Hämta instanserna med en viss status (till exempel körs eller slutfört). Kan ge flera (blankstegsavgränsad) status.
* (valfritt): **`top`** Antal instanser som hämtats per begäran. Standardvärdet är 10.
* (valfritt): **`continuation-token`** En token för att ange vilken sida eller vilket avsnitt av instanser som ska hämtas. Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

Om du inte anger några filter (`created-after`, `created-before`eller `runtime-status`) hämtar `top` kommandot bara instanser, utan hänsyn till körnings status eller skapande tid.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Avsluta instanser

Om du har en Dirigerings instans som tar för lång tid att köra, eller om du bara behöver stoppa den innan den har slutförts av någon anledning, kan du välja att avsluta den.

Du kan använda [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) -metoden för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen (.net) eller `terminate` metoden för `DurableOrchestrationClient` klassen (Java Script). De två parametrarna är en `instanceId` och en `reason` sträng som skrivs till loggar och instans status. En avbruten instans stoppas så snart den når nästa `await` (.net) eller `yield` (JavaScript) punkt, eller så avslutas den omedelbart om den redan finns på en `await` eller `yield`.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Instans avslutningen sprids inte för närvarande. Aktivitets funktioner och under dirigering körs till slut för ande, oavsett om du har avslutat Orchestration-instansen som anropade dem.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också avsluta en Dirigerings instans direkt genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable terminate` . Det tar följande parametrar:

* (krävs): **`id`** ID för Orchestration-instansen som ska avslutas.
* (valfritt): **`reason`** Orsak till uppsägning.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

Följande kommando avslutar en Dirigerings instans med ID: t 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Skicka händelser till instanser

I vissa fall är det viktigt att dina Orchestrator-funktioner kan vänta och lyssna efter externa händelser. Detta omfattar [övervaknings funktioner](durable-functions-overview.md#monitoring) och funktioner som väntar på [mänsklig interaktion](durable-functions-overview.md#human).

Skicka händelse meddelanden till att köra instanser med [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen (.net `raiseEvent` ) eller metoden för `DurableOrchestrationClient` klassen (Java Script). Instanser som kan hantera dessa händelser är de som väntar på ett anrop till [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.net) eller `waitForExternalEvent` (Java Script).

Parametrarna för [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) och `raiseEvent` (Java Script) är följande:

* **InstanceID**: Instansens unika ID.
* **EventName**: Namnet på händelsen som ska skickas.
* **EventData**: En JSON-serialiserbar nytto last att skicka till instansen.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Om det inte finns någon Dirigerings instans med angivet instans-ID, eller om instansen inte väntar på det angivna händelse namnet, ignoreras händelse meddelandet. Mer information om det här problemet finns i [GitHub-problemet](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också utlösa en händelse till en Orchestration-instans direkt genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable raise-event` . Det tar följande parametrar:

* (krävs): **`id`** ID för Orchestration-instansen.
* (valfritt): **`event-name`** Namn på händelsen som ska upphöjas. Standardvärdet är `$"Event_{RandomGUID}"`.
* (valfritt): **`event-data`** Data som ska skickas till Orchestration-instansen. Detta kan vara sökvägen till en JSON-fil eller så kan du ange data direkt på kommando raden.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Vänta på slut för ande av dirigering

I långvariga dirigeringar kanske du vill vänta och få resultatet av ett Dirigerings resultat. I dessa fall är det också användbart att kunna definiera en tids gräns för dirigering. Om tids gränsen överskrids ska dirigeringens tillstånd returneras i stället för resultatet.

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen visar ett [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) -API i .net. Du kan använda detta API för att hämta faktiska utdata från en Dirigerings instans synkront. I Java Script `DurableOrchestrationClient` visar klassen ett `waitForCompletionOrCreateCheckStatusResponse` API för samma ändamål. När de inte har angetts använder metoderna ett standardvärde på 10 sekunder för `timeout`och 1 sekund för. `retryInterval`  

Här är ett exempel på en HTTP-utlösnings funktion som visar hur du använder det här API: et:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Anropa funktionen med följande rad. Använd 2 sekunder för timeout och 0,5 sekunder för återförsöksintervall:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Det finns två fall beroende på hur lång tid det tar att hämta svaret från Orchestration-instansen:

* Dirigerings instanserna slutförs inom den angivna tids gränsen (i det här fallet 2 sekunder) och svaret är den faktiska Dirigerings instansens utdata som levereras synkront:

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

* Dirigerings instanserna kan inte slutföras inom den angivna tids gränsen och svaret är standard som beskrivs i [http API URL-identifiering](durable-functions-http-api.md):

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
> Formatet för webhook-URL: erna kan variera beroende på vilken version av Azure Functions-värden som du kör. Föregående exempel är för Azure Functions 2. x-värden.

## <a name="retrieve-http-management-webhook-urls"></a>Hämta URL: er för HTTP Management webhook

Du kan använda ett externt system för att övervaka eller utlösa händelser till en dirigering. Externa system kan kommunicera med Durable Functions via webhook-URL: er som ingår i standardsvaret som beskrivs i [http API URL-identifiering](durable-functions-http-api.md). Webhook-URL: er kan dock också nås via programmering i Orchestration-klienten eller i en aktivitets funktion. Det gör du med hjälp av [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) -metoden för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen (.net) eller `createHttpManagementPayload` metoden för `DurableOrchestrationClient` klassen (Java Script).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) och `createHttpManagementPayload` ha en parameter:

* **InstanceID**: Instansens unika ID.

Metoderna returnerar en instans av [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.net) eller ett objekt (Java Script) med följande sträng egenskaper:

* **ID**: Instans-ID: t för dirigeringen (ska vara detsamma som `InstanceId` indatatypen).
* **StatusQueryGetUri**: Status-URL: en för Orchestration-instansen.
* **SendEventPostUri**: URL: en "öka händelse" för Orchestration-instansen.
* **TerminatePostUri**: "Avsluta"-URL: en för Orchestration-instansen.
* **RewindPostUri**: URL: en "spola tillbaka" för Orchestration-instansen.

Aktivitets funktioner kan skicka en instans av dessa objekt till externa system för att övervaka eller utlösa händelser till en dirigering:

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

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

## <a name="rewind-instances-preview"></a>Spola tillbaka instanser (förhands granskning)

Om du har ett Dirigerings fel av oväntad anledning kan du *spola tillbaka* instansen till ett tidigare fungerande tillstånd med hjälp av ett API som skapats för detta ändamål.

> [!NOTE]
> Detta API är inte avsett att vara en ersättning för korrekt fel hantering och principer för återförsök. I stället är det endast avsett att användas i fall där Orchestration-instanser inte kan utföra oväntade orsaker. Mer information om fel hantering och principer för återförsök finns i avsnittet [fel hantering](durable-functions-error-handling.md) .

Använd API: et för [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) ( `rewindAsync` .net) eller (Java Script) för att försätta dirigeringen i *körnings* läge igen. Kör körnings felen för aktiviteten eller under dirigeringen som orsakade ett Orchestration-haveri.

Anta till exempel att du har ett arbets flöde som innehåller en serie med [mänsklig godkännande](durable-functions-overview.md#human). Anta att det finns en serie aktivitets funktioner som meddelar någon att deras godkännande krävs och väntar på svar i real tid. När alla godkännande aktiviteter har fått svar eller nått tids gränsen, antar vi att en annan aktivitet Miss lyckas på grund av en fel konfiguration i programmet, t. ex. en ogiltig databas anslutnings sträng. Resultatet är ett djupgående problem i arbets flödet. Med API: et `rewindAsync` (.net)eller(JavaScript)kanenprogramadministratöråtgärdakonfigurationsfeletochspolatillbakadenmisslyckadedirigeringentillstatusenomedelbartförefelet.`RewindAsync` Ingen av de mänskliga interaktions stegen måste godkännas igen och dirigeringen kan nu slutföras.

> [!NOTE]
> Funktionen *spola tillbaka* stöder inte omspolning av Orchestration-instanser som använder varaktiga timers.

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

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan också spola en Orchestration-instans direkt genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable rewind` . Det tar följande parametrar:

* (krävs): **`id`** ID för Orchestration-instansen.
* (valfritt): **`reason`** Orsak till omvridning av Orchestration-instansen.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Rensa instans historik

Om du vill ta bort alla data som är associerade med ett dirigering kan du rensa instans historiken. Till exempel kanske du vill ta bort Azure Table-rader och stora blobar för meddelanden, om de finns. Det gör du med hjälp av [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) -API: et.

> [!NOTE]
> API: t är för närvarande endast tillgängligt för C# `PurgeInstanceHistoryAsync`

 Metoden har två överlagringar. Den första enda rensningen av historiken med ID: t för Orchestration-instansen:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

I det andra exemplet visas en timer-utlöst funktion som rensar historiken för alla Dirigerings instanser som har slutförts efter angivet tidsintervall. I det här fallet tar den bort data för alla instanser som slutförts 30 eller fler dagar sedan. Det är schemalagt att köras en gång per dag, kl. 12:

### <a name="c"></a>C#

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
> För att processen ska kunna utföras måste körnings status vara **slutförd**, **avslutas**eller **Miss**lyckas.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du kan rensa en Dirigerings instanss historik genom att använda kommandot [Azure Functions Core tools](../functions-run-local.md) `durable purge-history` . I likhet med det C# andra exemplet i föregående avsnitt rensar den historiken för alla Dirigerings instanser som skapats under ett angivet tidsintervall. Du kan filtrera rensade instanser ytterligare efter körnings status. Kommandot har flera parametrar:

* (valfritt): **`created-after`** Rensa historiken för instanser som skapats efter detta datum/tid (UTC). ISO 8601 formaterade datetimes har godkänts.
* (valfritt): **`created-before`** Rensa historiken för instanser som skapats före detta datum/tid (UTC). ISO 8601 formaterade datetimes har godkänts.
* (valfritt): **`runtime-status`** Rensa historiken för instanser med en viss status (till exempel körs eller slutfört). Kan ge flera (blankstegsavgränsad) status.
* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

Följande kommando tar bort historiken för alla misslyckade instanser som skapats före den 14 november 2018 vid 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Ta bort en aktivitets hubb

Med hjälp av kommandot [Azure Functions Core tools](../functions-run-local.md) `durable delete-task-hub` kan du ta bort alla lagrings artefakter som är associerade med ett visst aktivitets nav. Detta inkluderar Azure Storage-tabeller, köer och blobbar. Kommandot har två parametrar:

* (valfritt): **`connection-string-setting`** Namnet på den program inställning som innehåller den lagrings anslutnings sträng som ska användas. Standardvärdet är `AzureWebJobsStorage`.
* (valfritt): **`task-hub-name`** Namnet på den Durable Functions aktivitets hubb som ska användas. Standardvärdet är `DurableFunctionsHub`. Den kan också anges i [Host. JSON](durable-functions-bindings.md#host-json), genom att använda DurableTask: HubName.

Följande kommando tar bort alla Azure Storage-data som är `UserTest` associerade med aktivitets navet.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versions hantering](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Inbyggd HTTP API-referens för instans hantering](durable-functions-http-api.md)