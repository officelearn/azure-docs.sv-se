---
title: Bindningar för varaktiga funktioner – Azure
description: Hur du använder utlösare och bindningar för tillägget varaktiga Functons för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: f9bf42e5e20a7d9e861d0c3354040e981bf3ef21
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987757"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för varaktiga funktioner (Azure Functions)

Den [varaktiga funktioner](durable-functions-overview.md) tillägget introducerar två nya utlösaren bindningar som styr körningen av orchestrator och aktivitet. Det introducerar också en utdatabindning som fungerar som en klient för varaktiga Functions-körning.

## <a name="orchestration-triggers"></a>Orchestration-utlösare

Orchestration-utlösare kan du redigera varaktiga orchestrator-funktioner. Den här utlösaren stödjer nya orchestrator-funktion instanser startas och återuppta befintliga orchestrator-funktion-instanser som ”väntar” en uppgift.

När du använder Visual Studio-verktyg för Azure Functions, orchestration-utlösaren konfigureras med hjälp av den [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET-attributet.

När du skriver orchestrator-funktioner i skriptspråk (till exempel i Azure portal), orchestration-utlösaren definieras av följande JSON-objekt i den `bindings` matris med de *function.json* fil:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` är namnet på dirigering. Detta är det värde som klienter måste använda för att starta nya instanser av den här orchestrator-funktion. Den här egenskapen är valfri. Om den inte anges används namnet på funktionen.

Den här bindningen för utlösare avsöker internt en serie med köer i standardkontot för lagring för funktionsappen. Dessa köer är interna implementeringsdetaljer för tillägg, vilket är anledningen till att de inte uttryckligen har konfigurerats i bindningsegenskaperna för.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här följer några anteckningar om orchestration-utlösare:

* **Single-threading** – en enda dispatcher-tråden används för alla orchestrator-funktion-körning på en enda värd-instans. Därför är det viktigt att säkerställa att orchestrator function-koden är effektivt och inte utför alla i/o. Det är också viktigt att se till att den här tråden utför inte någon asynkroniseringsjobbet utom när väntar på på varaktiga funktioner-specifika aktivitetstyper.
* **Poison meddelandehantering** -det finns inget skadligt meddelande-stöd i orchestration-utlösare.
* **Meddelandet synlighet** -meddelanden för Orchestration-utlösare kan tagits bort från kön och hålls osynliga för en konfigurerbar varaktighet. Visningen av de här meddelandena förnyas automatiskt så länge funktionsappen är aktiv och felfri.
* **Returvärden** -returnera värden serialiserat till JSON och sparats till orchestration-historiktabellen i Azure Table storage. Dessa returvärden kan efterfrågas av orchestration-klienten bindning, som beskrivs senare.

> [!WARNING]
> Orchestrator-funktioner ska aldrig använda några indata eller utdatabindningar än orchestration utlösa bindning. Detta har kan orsaka problem med tillägget varaktiga uppgiften eftersom bindningar inte kan följa single-threading och i/o-regler.

### <a name="trigger-usage"></a>Utlösaren användning

Orchestration-utlösaren bindningen stöder både indata och utdata. Här följer några saker att känna till om indata och utdata hantering:

* **indata** -Orchestration-funktioner stöder bara [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) som en parametertyp. Deserialisering av indata direkt i i funktionssignaturen stöds inte. Kod måste använda den [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metod för att hämta orchestrator funktionsin. Dessa indata måste vara JSON-serialiserbara typer.
* **matar ut** -Orchestration-utlösare stöder utdatavärden som indata. Returvärdet för funktionen används för att tilldela värdet och måste vara JSON-serialiserbara. Om en funktion returnerar `Task` eller `void`, ett `null` värdet kommer att sparas som utdata.

### <a name="trigger-sample"></a>Exempel för utlösare

Följande är ett exempel på hur den enklaste ”Hello World” orchestrator-funktionen kan se ut:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript-initierare bör använda `return`. Den `durable-functions` biblioteket tar hand om anropar den `context.done` metoden.

De flesta funktioner i orchestrator anropar Aktivitetsfunktioner, så här är en ”Hello World”-exempel som visar hur du anropar en funktion för aktivitet:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Utlösare för aktivitet

Utlösaren aktivitet kan du redigera funktioner som anropas av orchestrator-funktioner.

Om du använder Visual Studio aktivitet utlösaren konfigureras med hjälp av den [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET-attributet. 

Om du använder VS Code eller Azure-portalen för utveckling, aktivitet utlösaren definieras av följande JSON-objekt i den `bindings` matris med *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` är namnet på aktiviteten. Detta är det värde som orchestrator-funktioner som använder för att anropa den här funktionen för aktiviteten. Den här egenskapen är valfri. Om den inte anges används namnet på funktionen.

Den här bindningen för utlösare avsöker internt en kö i standardkontot för lagring för funktionsappen. Den här kön är en intern implementeringsdetalj för tillägg, vilket är anledningen till att den inte explicit konfigurerad i bindningsegenskaperna för.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här följer några anteckningar om utlösaren aktivitet:

* **Threading** – till skillnad från orchestration-utlösaren utlösare för aktiviteten inte har några begränsningar gällande threading eller i/o. De kan hanteras som vanlig funktioner.
* **Poison meddelandehantering** -det finns inget skadligt meddelande-stöd i aktiviteten utlösare.
* **Meddelandet synlighet** -aktivitet utlösarmeddelanden kan tagits bort från kön och hålls osynliga för en konfigurerbar varaktighet. Visningen av de här meddelandena förnyas automatiskt så länge funktionsappen är aktiv och felfri.
* **Returvärden** -returnera värden serialiserat till JSON och sparats till orchestration-historiktabellen i Azure Table storage.

> [!WARNING]
> Lagringserverdel för Aktivitetsfunktioner är en implementeringsdetalj och användarkod bör inte samverka med dessa entiteter direkt.

### <a name="trigger-usage"></a>Utlösaren användning

Aktivitet utlösaren bindningen stöder både indata och utdata, precis som orchestration-utlösaren. Här följer några saker att känna till om indata och utdata hantering:

* **indata** -Aktivitetsfunktioner använda [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som en parametertyp. Alternativt kan en aktivitet funktion deklareras med parametern typer som är JSON-serialiserbara. När du använder `DurableActivityContext`, kan du anropa [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) att hämta och deserialisera funktionen aktivitet indata.
* **matar ut** -Aktivitetsfunktioner stöd utdatavärden samt indata. Returvärdet för funktionen används för att tilldela värdet och måste vara JSON-serialiserbara. Om en funktion returnerar `Task` eller `void`, ett `null` värdet kommer att sparas som utdata.
* **metadata** -Aktivitetsfunktioner kan bindas till en `string instanceId` parametern för att hämta instans-ID på överordnad dirigering.

### <a name="trigger-sample"></a>Exempel för utlösare

Följande är ett exempel på hur en enkel ”Hello World” aktivitet funktion kan se ut:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
module.exports = function(context) {
    context.done(null, `Hello ${context.bindings.name}!`);
};
```

Standard-parametertypen för den `ActivityTriggerAttribute` bindningen är `DurableActivityContext`. Dock aktivitet utlösare också stöd för bindning direkt till JSON-serializeable typer (inklusive primitiva typer), så att samma funktion kan förenklas som följer:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

```javascript
module.exports = function(context, name) {
    context.done(null, `Hello ${name}!`);
};
```

### <a name="passing-multiple-parameters"></a>Skicka flera parametrar 

Det går inte att skicka flera parametrar i en aktivitet funktion direkt. I det här fallet är rekommendationen att skicka in en matris med objekt eller använda [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objekt.

I följande exempel använder nya funktioner i [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) lagts till med [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university 
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Orkestreringsklient

Orkestreringsklient bindning kan du skriva funktioner som interagerar med orchestrator-funktioner. Du kan till exempel arbeta med orchestration-instanser på följande sätt:
* Starta dem.
* Fråga efter deras status.
* Avsluta dem.
* Skicka händelser till dem när de körs.

Om du använder Visual Studio kan du binda till orchestration-klienten med hjälp av den [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET-attributet.

Om du använder skriptspråk (t.ex. *.csx* eller *.js* filer) för utveckling, orkestrering utlösaren definieras av följande JSON-objekt i den `bindings` matris med  *Function.JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` – Används i scenarier där flera funktionsappar dela samma lagringskonto, men de måste isoleras från varandra. Om inte anges används standardvärdet från `host.json` används. Det här värdet måste matcha det värde som används av mål orchestrator-funktioner.
* `connectionName` – Namnet på en appinställning som innehåller en anslutningssträng för lagringskonto. Storage-kontot som representeras av den här anslutningssträngen måste vara samma konto används av mål orchestrator-funktioner. Om den inte anges används standard storage-konto-anslutningssträngen för funktionsappen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar dessa egenskaper och förlitar sig på standardbeteendet.

### <a name="client-usage"></a>Klientanvändning

I C#-funktioner, vanligtvis bind till `DurableOrchestrationClient`, vilket ger dig fullständig åtkomst till alla klientens API: er som stöds av varaktiga funktioner. API: er för klient-objektet är:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Du kan binda till `IAsyncCollector<T>` där `T` är [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) eller `JObject`.

Se den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-dokumentationen för mer information om dessa åtgärder.

### <a name="client-sample-visual-studio-development"></a>Klienten exemplet (Visual Studio-utveckling)

Här är en exempel-kö-utlöst funktion som startar en ”HelloWorld” dirigering.

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

### <a name="client-sample-not-visual-studio"></a>Klienten exemplet (inte Visual Studio)

Om du inte använder Visual Studio för utveckling, kan du skapa följande *function.json* fil. Det här exemplet visar hur du konfigurerar en funktion som utlöses av kön som använder klient varaktiga orchestration-bindning:

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

Följande är exempel på språkspecifika som startar du nya instanser av orchestrator-funktion.

#### <a name="c-sample"></a>C#-exempel

I följande exempel visas hur du använder den beständiga orkestreringsklient bindning för att starta en ny funktion-instans från en C#-skriptfunktion:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-exempel

I följande exempel visas hur du använder den beständiga orkestreringsklient bindning för att starta en ny funktion-instans från en JavaScript-funktion:

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

Mer information om hur du startar instanser finns i [instans management](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om kontrollpunkter och återuppspelning beteenden](durable-functions-checkpointing-and-replay.md)

