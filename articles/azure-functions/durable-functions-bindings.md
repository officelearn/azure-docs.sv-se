---
title: "Bindningar för beständig funktioner – Azure"
description: "Hur du använder utlösare och bindningar för beständig Functons-tillägg för Azure Functions."
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
ms.openlocfilehash: 01016294c3ef6fd904a7582e4f9c16ef19330a20
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för beständig funktioner (Azure-funktioner)

Den [varaktiga funktioner](durable-functions-overview.md) tillägget introducerar två nya utlösaren bindningar som styr körningen av orchestrator och aktivitet. Det skapar också en bindning för utdata som fungerar som en klient för beständig Functions-runtime.

## <a name="orchestration-triggers"></a>Orchestration-utlösare

Orchestration-utlösare gör att du kan redigera varaktiga orchestrator-funktioner. Den här utlösaren stöder startar nya instanser av orchestrator-funktionen och återuppta befintliga orchestrator funktionen instanser som ”väntar” en aktivitet.

När du använder Visual Studio-verktygen för Azure Functions orchestration-utlösaren konfigureras med hjälp av den [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET-attributet.

När du skriver orchestrator-funktioner i skriptspråk (till exempel i Azure-portalen) orchestration-utlösaren definieras av följande JSON-objekt i den `bindings` matris med de *function.json* fil:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`är namnet på orchestration. Detta är det värde som klienter måste använda när de vill börja nya instanser av den här funktionen för orchestrator. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.
* `version`är en versionsetikett för orchestration. Klienter som startar en ny instans av en orchestration måste innehålla matchande versionsetiketten. Den här egenskapen är valfri. Om inget annat anges, används en tom sträng. Mer information om versioner finns [versionshantering](durable-functions-versioning.md).

> [!NOTE]
> Ange värden för `orchestration` eller `version` egenskaper rekommenderas inte just nu.

Internt avsöker bindningen utlösare en serie av köer på standardkontot för lagring för funktionen appen. Dessa köer är internt implementeringsinformation om filnamnstillägget, vilket är anledningen till att de inte uttryckligen har konfigurerats i bindningsegenskaperna för.

### <a name="trigger-behavior"></a>Utlösaren beteende

Här följer några anteckningar om orchestration-utlösare:

* **Single-threading** – en enda dispatcher-tråden används för alla orchestrator-funktionen körning på en enda värd-instans. Därför är det viktigt att kontrollera att orchestrator-Funktionskoden är effektivt och inte utför alla i/o. Det är också viktigt att se till att den här tråden inte utföra asynkrona arbete utom när väntar på på varaktiga funktioner-specifik aktivitetstyper.
* **Poising meddelandehantering** -det finns inget stöd för skadligt meddelande i orchestration-utlösare.
* **Meddelande synlighet** -Orchestration utlösarmeddelanden togs bort från kön och hålls osynliga för en konfigurerbar tidsperiod. Synligheten för dessa meddelanden förnyas automatiskt så länge funktionen appen är körs och är felfria.
* **Returvärden** -returnera värden serialiseras till JSON och beständig historiktabellen orchestration i Azure Table storage. Dessa returvärden kan efterfrågas av orchestration-klienten bindning, beskrivs senare.

> [!WARNING]
> Orchestrator-funktioner ska aldrig ha alla indata eller utdata bindningar än orchestration utlösa bindning. Detta har kan orsaka problem med beständiga Task-tillägget eftersom dessa bindningar inte kan följa single-threading och i/o-regler.

### <a name="trigger-usage"></a>Utlösaren användning

Orchestration-utlösaren bindningen stöder både inmatningar och matar ut. Här följer några saker att veta om indata och utdata hantering:

* **indata** -Orchestration-funktioner stöder bara [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) som parametertyp. Deserialisering indata direkt i funktionssignatur stöds inte. Koden måste använda den [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metod för att hämta orchestrator indata för funktionen. Dessa indata måste vara JSON-serialiserbara typer.
* **matar ut** -Orchestration utlösare stöder utdatavärden som indata. Returvärdet för funktionen används för att tilldela värdet och måste vara JSON-serialiserbara. Om en funktion returnerar `Task` eller `void`, en `null` värdet kommer att sparas som utdata.

> [!NOTE]
> Orchestration-utlösare stöds endast i C# just nu.

### <a name="trigger-sample"></a>Utlösaren exempel

Följande är ett exempel på hur funktionen enklaste ”Hello World” C# orchestrator kan se ut:

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

De flesta funktioner i orchestrator anropa andra funktioner, så här är en ”Hello World”-exempel som visar hur du anropa en funktion:

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = await context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

## <a name="activity-triggers"></a>Utlösare för aktiviteten

Aktiviteten utlösaren kan du redigera funktioner som anropas av orchestrator-funktioner.

Om du använder Visual Studio aktivitet utlösaren konfigureras med hjälp av den [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET-attributet. 

Om du använder Azure-portalen för utveckling, aktivitet utlösaren definieras av följande JSON-objekt i den `bindings` matris med *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`är namnet på aktiviteten. Detta är det värde som orchestrator-funktioner som använder för att anropa den här funktionen för aktiviteten. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.
* `version`är en versionsetikett för aktiviteten. Orchestrator-funktioner som anropar en aktivitet måste innehålla matchande versionsetiketten. Den här egenskapen är valfri. Om inget annat anges, används en tom sträng. Mer information finns i [versionshantering](durable-functions-versioning.md).

> [!NOTE]
> Ange värden för `activity` eller `version` egenskaper rekommenderas inte just nu.

Den här bindningen utlösare avsöker internt en kö i standardkontot för lagring för funktionen appen. Den här kön är en intern implementering information om filnamnstillägget, vilket är anledningen till det inte är uttryckligen har konfigurerats i bindningsegenskaperna för.

### <a name="trigger-behavior"></a>Utlösaren beteende

Här följer några anteckningar om utlösaren aktiviteten:

* **Trådmodell** -till skillnad från orchestration-utlösaren aktivitet utlösare inte har några begränsningar runt trådmodell eller i/o. De kan hanteras som vanlig funktioner.
* **Poising meddelandehantering** -det finns inget stöd för skadligt meddelande i aktiviteten utlösare.
* **Meddelande synlighet** -aktiviteten utlösarmeddelanden togs bort från kön och hålls osynliga för en konfigurerbar tidsperiod. Synligheten för dessa meddelanden förnyas automatiskt så länge funktionen appen är körs och är felfria.
* **Returvärden** -returnera värden serialiseras till JSON och beständig historiktabellen orchestration i Azure Table storage.

> [!WARNING]
> Lagringserverdel för aktiviteten funktioner är en implementering detaljer och användarkod bör inte samverka med entiteterna lagring direkt.

### <a name="trigger-usage"></a>Utlösaren användning

Aktiviteten utlösaren bindningen stöder både inmatningar och matar ut precis som orchestration-utlösare. Här följer några saker att veta om indata och utdata hantering:

* **indata** -aktiviteten funktioner använder internt [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som parametertyp. Alternativt kan en funktion för aktiviteten deklareras med parametern typer som är JSON-serialiserbara. När du använder `DurableActivityContext`, kan du anropa [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) att hämta och funktionen aktiviteten att deserialisera indata.
* **matar ut** -aktiviteten utlösare stöder utdatavärden som indata. Returvärdet för funktionen används för att tilldela värdet och måste vara JSON-serialiserbara. Om en funktion returnerar `Task` eller `void`, en `null` värdet kommer att sparas som utdata.
* **metadata** -aktiviteten funktioner kan bindas till en `string instanceId` parametern för att hämta instans-ID för överordnad orchestration.

> [!NOTE]
> Aktiviteten utlösare stöds inte i Node.js-funktioner.

### <a name="trigger-sample"></a>Utlösaren exempel

Följande är ett exempel på hur en enkel ”Hello World” C# aktivitet funktion kan se ut:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Parametern standardtypen för den `ActivityTriggerAttribute` bindning är `DurableActivityContext`. Dock aktivitet utlösare också stöd för bindning direkt till JSON-serializeable typer (inklusive primitiva typer), så samma funktion kan förenklas som följer:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

## <a name="orchestration-client"></a>Orchestration-klient

Orchestration-klienten bindning kan du skriva funktioner som interagerar med orchestrator-funktioner. Du kan till exempel fungera på orchestration-instanser på följande sätt:
* Starta dem.
* Fråga efter deras status.
* Avsluta dem.
* Skicka händelser till dem när de kör.

Om du använder Visual Studio kan du binda till orchestration-klienten med hjälp av den [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET-attributet.

Om du använder skriptspråk (t.ex. *.csx* filer) för utveckling, orchestration-utlösaren definieras av följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub`– Används i scenarier där flera funktionen appar delar samma lagringskonto men måste isoleras från varandra. Om inget anges standardvärdet från `host.json` används. Det här värdet måste matcha det värde som används av orchestrator-funktioner för målet.
* `connectionName`-Namnet på en appinställning som innehåller en anslutningssträng för lagring. Storage-kontot som representeras av den här anslutningssträngen måste vara samma som används av orchestrator-funktioner för målet. Om inget anges används standard-anslutningssträngen för funktionen appen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar dessa egenskaper och förlitar sig på standardbeteendet.

### <a name="client-usage"></a>Klientanvändning

I C#-funktioner, du vanligtvis binda finns till `DurableOrchestrationClient`, som ger dig fullständig åtkomst till alla klient-API: er som stöds av beständiga funktioner. API: er på klientobjekt är:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Du kan också binda till `IAsyncCollector<T>` där `T` är [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) eller `JObject`.

Finns det [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-dokumentationen för mer information om dessa åtgärder.

### <a name="client-sample-visual-studio-development"></a>Klienten exempel (Visual Studio-utveckling)

Här är ett exempel kön funktion som utlöses som startar en ”HelloWorld” orchestration.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Klienten exempel (inte Visual Studio)

Om du inte använder Visual Studio för utveckling, kan du skapa följande function.json-fil. Det här exemplet visar hur du konfigurerar en funktion som utlöses av kö som använder beständiga orchestration klienten bindning:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

Följande är exempel på vissa språk som startar du nya instanser av orchestrator-funktionen.

#### <a name="c-sample"></a>C#-exempel

I följande exempel visas hur du använder beständiga orchestration klienten bindning för att starta en ny instans av funktionen från en C#-skriptfunktion:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>Node.js-exempel

I följande exempel visas hur du använder beständiga orchestration klienten bindning för att starta en ny instans av funktionen från en Node.js-funktion:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Mer information om hur du startar instanser finns i [instansen management](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om kontrollpunkter och replay-beteenden](durable-functions-checkpointing-and-replay.md)

