---
title: Hantera instanser i varaktiga funktioner - Azure
description: Lär dig hur du hanterar instanser i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476826"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Hantera instanser i varaktiga funktioner i Azure

Om du använder tillägget [Varaktiga funktioner](durable-functions-overview.md) för Azure Functions, eller vill börja göra det, kontrollerar du att du får ut det bästa av det. Du kan optimera orchestration-instanserna för varaktiga funktioner genom att lära dig mer om hur du hanterar dem. Den här artikeln går till information om varje instanshanteringsåtgärd.

Du kan till exempel starta och avsluta instanser och du kan fråga instanser, inklusive möjligheten att fråga alla instanser och frågeinstanser med filter. Dessutom kan du skicka händelser till instanser, vänta på att orchestration har slutförts och hämta HTTP-hantering webhook-url:er. Den här artikeln beskriver även andra hanteringsåtgärder, inklusive att spola tillbaka instanser, rensa instanshistorik och ta bort en aktivitetsnav.

I varaktiga funktioner har du alternativ för hur du vill implementera var och en av dessa hanteringsåtgärder. Den här artikeln innehåller exempel som använder [Azure Functions Core Tools](../functions-run-local.md) för både .NET (C#) och JavaScript.

## <a name="start-instances"></a>Starta instanser

Det är viktigt att kunna starta en instans av orkestrering. Detta görs ofta när du använder en varaktig funktionsbindning i en annan funktions utlösare.

Metoden `StartNewAsync` (.NET) `startNew` eller (JavaScript) på [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) startar en ny instans. Internt, den här metoden följer ett meddelande i kontrollkön, som sedan utlöser början av en funktion med det angivna namnet som använder [orchestration utlösa bindande](durable-functions-bindings.md#orchestration-trigger).

Den här asynkron åtgärden slutförs när orchestration-processen har schemalagts.

Parametrarna för att starta en ny orchestration-instans är följande:

* **Namn**: Namnet på orchestrator-funktionen som ska schemaläggas.
* **Indata**: Alla JSON-serialiserbara data som ska skickas som indata till orchestrator-funktionen.
* **InstanceId**: (Valfritt) Det unika ID:t för instansen. Om du inte anger den här parametern används ett slumpmässigt ID med metoden.

> [!TIP]
> Använd en slumpmässig identifierare för instans-ID. Slumpmässiga instans-ID:n hjälper till att säkerställa en jämn belastningsfördelning när du skalar orchestrator-funktioner över flera virtuella datorer. Rätt tid att använda icke-slumpmässiga instans-ID är när ID måste komma från en extern källa, eller när du implementerar [singleton orchestrator-mönstret.](durable-functions-singletons.md)

Följande kod är en exempelfunktion som startar en ny orkestreringsinstans:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Om inget annat anges använder exemplen på den här sidan HTTP-utlösaren med följande function.json.

**funktion.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Det här exemplet är inriktat på varaktiga funktioner version 2.x. Använd `orchestrationClient` i version 1.x `durableClient`i stället för .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Du kan också starta en instans direkt med kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable start-new` Det tar följande parametrar:

* (obligatoriskt): Namnet på den funktion som ska startas. ** `function-name` **
* (valfritt) : Ingång till funktionen, antingen infogad eller via en JSON-fil. ** `input` ** För filer lägger du till ett prefix i sökvägen till filen med `@`, till exempel `@path/to/file.json`.
* (valfritt) : ID för orkestreringsinstansen. ** `id` ** Om du inte anger den här parametern används ett slumpmässigt GUID i kommandot.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standard är AzureWebJobsStorage.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standard är DurableFunctionsHub. Du kan också ange detta i [host.json](durable-functions-bindings.md#host-json) med hjälp av durableTask:HubName.

> [!NOTE]
> Core Tools-kommandon förutsätter att du kör dem från rotkatalogen för en funktionsapp. Om du uttryckligen `connection-string-setting` `task-hub-name` anger och parametrar kan du köra kommandona från valfri katalog. Även om du kan köra dessa kommandon utan att en funktionsappvärd körs, kanske du upptäcker att du inte kan observera vissa effekter om inte värden körs. `start-new` Kommandot följer till exempel ett startmeddelande i målaktivitetshubben, men orkestreringen körs inte om det inte finns en funktionsappvärdprocess som körs som kan bearbeta meddelandet.

Följande kommando startar funktionen HelloWorld och skickar innehållet `counter-data.json` i filen till den:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Frågeinstanser

Som en del av din strävan att hantera dina orkestreringar, behöver du troligen samla in information om status för en orkestrering instans (till exempel om det har slutförts normalt eller misslyckats).

Metoden `GetStatusAsync` (.NET) `getStatus` eller (JavaScript) på [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) ifrågasätter statusen för en orchestration-instans.

Det tar `instanceId` en `showHistory` (obligatorisk), `showHistoryOutput` (valfritt), (valfritt) och `showInput` (valfritt) som parametrar.

* **`showHistory`**: Om `true`det är inställt på innehåller svaret körningshistoriken.
* **`showHistoryOutput`**: Om `true`den är inställd på innehåller körningshistoriken aktivitetsutdata.
* **`showInput`**: Om `false`det är inställt på innehåller svaret inte indata från funktionen. Standardvärdet är `true`.

Metoden returnerar ett objekt med följande egenskaper:

* **Namn**: Namnet på orchestrator-funktionen.
* **InstanceId**: Instans-ID för orkestrering (bör vara samma som `instanceId` indata).
* **CreatedTime**: Den tidpunkt då orchestrator-funktionen började köras.
* **LastUpdatedTime**: Den tidpunkt då orkestreringen senast kontrollpunkter.
* **Ingång**: Indata för funktionen som ett JSON-värde. Det här fältet är `showInput` inte ifyllt om det är falskt.
* **Anpassad status:** Anpassad orkestreringsstatus i JSON-format.
* **Utdata**: Utdata för funktionen som ett JSON-värde (om funktionen har slutförts). Om orchestrator-funktionen misslyckades innehåller den här egenskapen felinformationen. Om orchestrator-funktionen har avslutats innehåller den här egenskapen orsaken till uppsägningen (om sådan finns).
* **RuntimeStatus**: Ett av följande värden:
  * **Väntar :** Instansen har schemalagts men har ännu inte börjat köras.
  * **Kör**: Instansen har börjat köras.
  * **Slutförd**: Instansen har slutförts normalt.
  * **ContinuedAsNew**: Instansen har startat om sig själv med en ny historik. Det här tillståndet är ett övergående tillstånd.
  * **Misslyckades**: Instansen misslyckades med ett fel.
  * **Avslutad**: Instansen stoppades abrupt.
* **Historia**: Utförandehistorien av orchestrationen. Det här fältet fylls bara i om `showHistory` det är inställt på `true`.

Den här `null` metoden returnerar `undefined` (.NET) eller (JavaScript) om instansen inte finns.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Det är också möjligt att få status för en orchestration-instans direkt, med hjälp av kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable get-runtime-status` Det tar följande parametrar:

* (obligatoriskt): ID för orkestreringsinstansen. ** `id` **
* (valfritt) : `true`Om det är inställt på innehåller svaret funktionens ingång. ** `show-input` ** Standardvärdet är `false`.
* (valfritt) : `true`Om det är inställt på innehåller svaret funktionens utdata. ** `show-output` ** Standardvärdet är `false`.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando hämtar status (inklusive indata och utdata) för en instans med ett orchestration instance-ID på 0ab8c55a6644d68a3a8b220b12d209c. Det förutsätter att du `func` kör kommandot från rotkatalogen i funktionsappen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Du kan `durable get-history` använda kommandot för att hämta historiken för en orchestration-instans. Det tar följande parametrar:

* (obligatoriskt): ID för orkestreringsinstansen. ** `id` **
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i host.json, med hjälp av durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Fråga alla instanser

I stället för att fråga en instans i orkestreringen i taget kan det vara mer effektivt att fråga dem alla på en gång.

Du kan `GetStatusAsync` använda metoden (.NET) eller `getStatusAll` (JavaScript) för att fråga status för alla orchestration-instanser. I .NET kan du `CancellationToken` skicka ett objekt om du vill avbryta det. Metoden returnerar objekt med samma `GetStatusAsync` egenskaper som metoden med parametrar.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Det är också möjligt att fråga instanser direkt, med hjälp av kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable get-instances` Det tar följande parametrar:

* (valfritt) : Det här kommandot stöder personsökning. ** `top` ** Den här parametern motsvarar antalet instanser som hämtas per begäran. Standardvärdet är 10.
* (valfritt) : En token som anger vilken sida eller vilket avsnitt av instanser som ska hämtas. ** `continuation-token` ** Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Frågeinstanser med filter

Vad händer om du egentligen inte behöver all information som en standardinstansfråga kan ge? Vad händer till exempel om du bara letar efter skapande tid för orkestrering eller orchestration runtime-status? Du kan begränsa frågan genom att använda filter.

Använd `GetStatusAsync` metoden (.NET) eller `getStatusBy` (JavaScript) för att hämta en lista över orchestration-instanser som matchar en uppsättning fördefinierade filter.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

I Azure Functions Core Tools kan `durable get-instances` du också använda kommandot med filter. Förutom de ovan nämnda `top` `continuation-token`, `connection-string-setting`, `task-hub-name` och parametrarna kan du`created-after` `created-before`använda `runtime-status`tre filterparametrar ( , och ).

* (valfritt) : Hämta de instanser som skapats efter detta datum/tid (UTC). ** `created-after` ** ISO 8601-formaterade datumtider accepteras.
* (valfritt) : Hämta de instanser som skapats före detta datum/tid (UTC). ** `created-before` ** ISO 8601-formaterade datumtider accepteras.
* (valfritt) : Hämta instanser med en viss status (till exempel löpning eller slutförd). ** `runtime-status` ** Kan ge flera (utrymme separerade) status.
* (valfritt) : Antal instanser som hämtats per begäran. ** `top` ** Standardvärdet är 10.
* (valfritt) : En token som anger vilken sida eller vilket avsnitt av instanser som ska hämtas. ** `continuation-token` ** Varje `get-instances` körning returnerar en token till nästa uppsättning instanser.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Om du inte tillhandahåller några`created-after` `created-before`filter `runtime-status`( , , `top` eller ), hämtar kommandot helt enkelt instanser, utan hänsyn till körningsstatus eller skapandetid.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Avsluta instanser

Om du har en orkestreringsinstans som tar för lång tid att köra, eller om du bara behöver stoppa den innan den är klar av någon anledning, har du möjlighet att avsluta den.

Du kan `TerminateAsync` använda metoden (.NET) eller `terminate` (JavaScript) för [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) för att avsluta instanser. De två parametrarna är en `instanceId` och en `reason` sträng, som skrivs till loggar och till instansstatus.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

En avslutad instans övergår `Terminated` så småningom till tillståndet. Denna övergång kommer dock inte att ske omedelbart. I stället kommer avsluta-åtgärden att köas i aktivitetshubben tillsammans med andra åtgärder för den instansen. Du kan använda [instansfråga](#query-instances) API:erna för att `Terminated` veta när en avslutad instans faktiskt har nått tillståndet.

> [!NOTE]
> Instansavslutning sprids för närvarande inte. Aktivitetsfunktioner och underorkestreringar körs till slutförande, oavsett om du har avslutat orchestration-instansen som anropade dem.

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Du kan också avsluta en orchestration-instans direkt med kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable terminate` Det tar följande parametrar:

* (obligatoriskt): ID för orchestration-instansen att avsluta. ** `id` **
* (frivillig uppgift): Skäl för uppsägning. ** `reason` **
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

Följande kommando avslutar en orkestreringsinstans med ett ID på 0ab8c55a6644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Skicka händelser till instanser

I vissa fall är det viktigt att orchestrator-funktionerna kan vänta och lyssna efter externa händelser. Detta inkluderar [övervaka funktioner](durable-functions-overview.md#monitoring) och funktioner som väntar på [mänsklig interaktion](durable-functions-overview.md#human).

Skicka händelsemeddelanden till instanser `RaiseEventAsync` som körs med metoden `raiseEvent` (.NET) eller (JavaScript)-metoden för [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client). Instanser som kan hantera dessa händelser är de `WaitForExternalEvent` som väntar på ett `waitForExternalEvent` anrop till (.NET) eller ger efter för ett (JavaScript)-anrop.

Parametrarna `RaiseEventAsync` för (.NET) och `raiseEvent` (JavaScript) är följande:

* **InstanceId**: Instansens unika ID.
* **EventName**: Namnet på händelsen som ska skickas.
* **EventData**: En JSON-serialisable nyttolast att skicka till instansen.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

> [!NOTE]
> Om det inte finns någon orkestreringsinstans med det angivna instans-ID:et ignoreras händelsemeddelandet. Om det finns en instans men den ännu inte väntar på händelsen lagras händelsen i instanstillståndet tills den är klar att tas emot och bearbetas.

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Du kan också höja en händelse till en orchestration-instans direkt med kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable raise-event` Det tar följande parametrar:

* (obligatoriskt): ID för orkestreringsinstansen. ** `id` **
* **`event-name`**: Namnet på händelsen att höja.
* (valfritt) : Data som ska skickas till orchestration-instansen. ** `event-data` ** Detta kan vara sökvägen till en JSON-fil, eller så kan du tillhandahålla data direkt på kommandoraden.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Standardvärdet är `DurableFunctionsHub`. Den kan också ställas in i [host.json](durable-functions-bindings.md#host-json), med hjälp av durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Vänta på att orkestrering har slutförts

I långvariga orkestreringar kanske du vill vänta och få resultatet av en orkestrering. I dessa fall är det också användbart att kunna definiera en timeout-period på orkestreringen. Om tidsgränsen överskrids ska orkestreringens tillstånd returneras i stället för resultatet.

Metoden `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) `waitForCompletionOrCreateCheckStatusResponse` eller (JavaScript) kan användas för att hämta den faktiska utdata från en orchestration-instans synkront. Som standard använder dessa metoder ett standardvärde `timeout`på 10 `retryInterval`sekunder för och 1 sekund för .  

Här är ett exempel HTTP-trigger funktion som visar hur du använder detta API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

Anropa funktionen med följande rad. Använd 2 sekunder för timeout och 0,5 sekunder för återförsöksintervallet:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Beroende på hur länge som krävs för att få svar från orchestration-instansen finns det två ärenden:

* Orchestration-instanserna slutförs inom den definierade timeouten (i det här fallet 2 sekunder) och svaret är den faktiska orchestration-instansutdata, levererad synkront:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Orchestration-instanserna kan inte slutföras inom den definierade tidsgränsen och svaret är det standardbeskrivna som beskrivs i [HTTP API URL-identifiering:](durable-functions-http-api.md)

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Formatet på webhook-url:erna kan variera beroende på vilken version av Azure Functions-värden du kör. Det föregående exemplet är för Azure Functions 2.0-värden.

## <a name="retrieve-http-management-webhook-urls"></a>Hämta URL:er för HTTP-hanteringswebbkrok

Du kan använda ett externt system för att övervaka eller höja händelser till en orkestrering. Externa system kan kommunicera med varaktiga funktioner via webhook-URL:erna som ingår i standardsvaret som beskrivs i identifieringen av [HTTP API-URL.](durable-functions-http-features.md#http-api-url-discovery) Webhook-url:erna kan alternativt nås programmässigt med hjälp av [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client). Metoderna `CreateHttpManagementPayload` `createHttpManagementPayload` (.NET) eller (JavaScript) kan användas för att hämta ett serialisable-objekt som innehåller dessa webhook-URL:er.

Metoderna `CreateHttpManagementPayload` (.NET) och `createHttpManagementPayload` (JavaScript) har en parameter:

* **instanceId**: Instansens unika ID.

Metoderna returnerar ett objekt med följande strängegenskaper:

* **Id**: Instans-ID för orkestrering (bör vara samma som `InstanceId` indata).
* **StatusQueryGetUri**: Status-URL:en för orchestration-instansen.
* **SendEventPostUri**: URL:en "raise event" för orchestration-instansen.
* **TerminatePostUri**: Url:en "avsluta" för orchestration-instansen.
* **PurgeHistoryDeleteUri**: Url:en "rensa historik" för orchestration-instansen.

Funktioner kan skicka instanser av dessa objekt till externa system för att övervaka eller höja händelser på motsvarande orkestreringar, vilket visas i följande exempel:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
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

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableActivityContext` måste `IDurableActivityContext`du använda `OrchestrationClient` i stället `DurableClient` för , du måste `DurableOrchestrationClient` använda attributet i stället för attributet och du måste använda parametertypen i stället för `IDurableOrchestrationClient`. Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

## <a name="rewind-instances-preview"></a>Spola tillbaka instanser (förhandsgranskning)

Om du har ett orkestreringsfel av en oväntad anledning kan du *spola tillbaka* instansen till ett tidigare felfritt tillstånd med hjälp av ett API som skapats för detta ändamål.

> [!NOTE]
> Det här API:et är inte avsett att ersätta korrekt principer för felhantering och återförsök. Den är snarare endast avsedd att användas i fall där orkestreringsinstanser misslyckas av oväntade skäl. Mer information om principer för felhantering och återförsök finns i artikeln [Felhantering.](durable-functions-error-handling.md)

Använd `RewindAsync` metoden (.NET) eller `rewind` (JavaScript) för [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) för att återföra orkestreringen till läget *Kör.* Den här metoden kommer också att köra aktiviteten eller underorkestreringskörningsfel som orsakade orkestreringsfelet.

Anta till exempel att du har ett arbetsflöde som omfattar en rad [mänskliga godkännanden](durable-functions-overview.md#human). Anta att det finns en serie aktivitetsfunktioner som meddelar någon om att deras godkännande behövs och väntar ut realtidssvaret. När alla godkännandeaktiviteter har fått svar eller tidsinställda, anta att en annan aktivitet misslyckas på grund av en felkonfiguration av ett program, till exempel en ogiltig databasanslutningssträng. Resultatet är ett orkestreringsfel djupt in i arbetsflödet. Med `RewindAsync` API:et `rewind` (.NET) eller (JavaScript) kan en programadministratör åtgärda konfigurationsfelet och spola tillbaka den misslyckade orkestreringen till tillståndet omedelbart före felet. Ingen av stegen för mänsklig interaktion behöver godkännas på ett korrekt sätt och orkestreringen kan nu slutföras.

> [!NOTE]
> *Bakåtfunktionen* stöder inte bakåtspolningsinstanser som använder varaktiga timers.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Du kan också spola tillbaka en orchestration-instans direkt med kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable rewind` Det tar följande parametrar:

* (obligatoriskt): ID för orkestreringsinstansen. ** `id` **
* (valfritt) : Orsak till att orchestration-instansen spolas tillbaka. ** `reason` **
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Som standard används aktivitetsnavnamnet i [filen host.json.](durable-functions-bindings.md#host-json)

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Rensa instanshistorik

Om du vill ta bort alla data som är associerade med en orkestrering kan du rensa instanshistoriken. Du kanske till exempel vill ta bort alla Azure Table-rader och stora meddelandeblobar som är associerade med en slutförd instans. Det gör du `PurgeInstanceHistoryAsync` genom att använda `purgeInstanceHistory` metoden (.NET) eller (JavaScript) för [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client).

Denna metod har två överbelastningar. Den första överbelastningen rensar historiken efter orchestration-instansens ID:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Se [Starta instanser](#javascript-function-json) för funktion.json-konfigurationen.

---

I nästa exempel visas en timerutlöst funktion som rensar historiken för alla orkestreringsinstanser som slutfördes efter det angivna tidsintervallet. I det här fallet tas data bort för alla instanser som slutfördes för 30 eller fler dagar sedan. Det är planerat att köras en gång per dag, klockan 12:00:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
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
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Metoden `purgeInstanceHistoryBy` kan användas för att villkorligt rensa instanshistorik för flera instanser.

**funktion.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Det här exemplet är inriktat på varaktiga funktioner version 2.x. Använd `orchestrationClient` i version 1.x `durableClient`i stället för .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> För att rensningshistorikåtgärden ska lyckas måste körningsstatusen för målinstansen vara **Slutförd**, **Avslutad**eller **Misslyckad**.

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Du kan rensa historiken för en orchestration-instans med kommandot [Azure Functions Core Tools.](../functions-run-local.md) `durable purge-history` I likhet med det andra C#-exemplet i föregående avsnitt rensas historiken för alla orkestreringsinstanser som skapats under ett angivet tidsintervall. Du kan filtrera bortrensade instanser ytterligare efter körningsstatus. Kommandot har flera parametrar:

* (valfritt) : Rensa historiken för instanser som skapats efter detta datum/tid (UTC). ** `created-after` ** ISO 8601-formaterade datumtider accepteras.
* (valfritt) : Rensa historiken för instanser som skapats före detta datum/tid (UTC). ** `created-before` ** ISO 8601-formaterade datumtider accepteras.
* (valfritt) : Rensa historiken för instanser med en viss status (till exempel köras eller slutförts). ** `runtime-status` ** Kan ge flera (utrymme separerade) status.
* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Som standard används aktivitetsnavnamnet i [filen host.json.](durable-functions-bindings.md#host-json)

Följande kommando tar bort historiken för alla misslyckade instanser som skapats före den 14 november 2018 klockan 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Ta bort en aktivitetsnav

Med kommandot [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` kan du ta bort alla lagringsartefakter som är associerade med en viss aktivitetsnav, inklusive Azure-lagringstabeller, köer och blobbar. Kommandot har två parametrar:

* (valfritt) : Namn på den programinställning som innehåller den lagringsanslutningssträng som ska användas. ** `connection-string-setting` ** Standardvärdet är `AzureWebJobsStorage`.
* (valfritt) : Namnet på uppgiftshubben Varaktiga funktioner som ska användas. ** `task-hub-name` ** Som standard används aktivitetsnavnamnet i [filen host.json.](durable-functions-bindings.md#host-json)

Följande kommando tar bort alla Azure-lagringsdata som är associerade med `UserTest` aktivitetshubben.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om hur du hanterar versionshantering](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Inbyggd HTTP API-referens för instanshantering](durable-functions-http-api.md)
