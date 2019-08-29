---
title: Bindningar för Durable Functions – Azure
description: Använda utlösare och bindningar för Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbd645ef9f5e687e71ce110fc84b8342e31defed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087541"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) -tillägget introducerar två nya Utlös ande bindningar som styr körningen av Orchestrator-och aktivitets funktioner. Den introducerar också en utgående bindning som fungerar som en klient för Durable Functions Runtime.

## <a name="orchestration-triggers"></a>Orchestration-utlösare

Genom Orchestration-utlösaren kan du skapa robusta Orchestrator-funktioner. Den här utlösaren stöder start av nya Orchestrator Function-instanser och återupptar befintliga Orchestrator-funktioner som väntar på en aktivitet.

När du använder Visual Studio-verktygen för Azure Functions konfigureras Orchestration-utlösaren med hjälp av attributet [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .net.

När du skriver Orchestrator-funktioner i skript språk (till exempel Java Script C# eller skript) definieras Orchestration-utlösaren av följande JSON-objekt i `bindings` matrisen för *Function. JSON* -filen:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`är namnet på dirigeringen. Detta är det värde som klienter måste använda när de vill starta nya instanser av den här Orchestrator-funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt denna Utlös ande bindning avsöker en serie med köer i standard lagrings kontot för Function-appen. Dessa köer är interna implementerings information om tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende vid utlösare

Här följer några anmärkningar om Dirigerings utlösaren:

* **Enkel tråds** -en enskild dispatcher-tråd används för alla Orchestrator-funktioner på en enda värd instans. Därför är det viktigt att se till att Orchestrator-funktions koden är effektiv och inte utför någon I/O. Det är också viktigt att se till att den här tråden inte gör något asynkront arbete, förutom vid väntan på Durable Functions-angivna uppgifts typer.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i Orchestration-utlösare.
* **Meddelande synlighet** – meddelanden om Orchestration-utlösare är i kö och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -retur värden serialiseras till JSON och sparas i tabellen Orchestration-historik i Azure Table Storage. Dessa retur värden kan frågas av Dirigerings klientens bindning, som beskrivs senare.

> [!WARNING]
> Orchestrator-funktioner bör aldrig använda eventuella indata eller utgående bindningar än bindningen för Dirigerings utlösaren. Detta har möjlighet att orsaka problem med det varaktiga aktivitets tillägget eftersom dessa bindningar inte kan följa reglerna för enkel trådning och I/O.

> [!WARNING]
> JavaScript Orchestrator-funktioner ska aldrig deklareras `async`.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Bindningen för Dirigerings utlösare stöder både indata och utdata. Här är några saker du behöver veta om indata och utdata:

* **indata** – .net-Orchestration-funktioner stöder endast [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) som parameter typ. Deserialisering av indata direkt i funktions signaturen stöds inte. Koden måste använda metoden [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) eller `getInput` (Java Script) för att hämta Orchestrator-funktionen indata. Dessa indata måste vara JSON-serialiserbara typer.
* **utdata** -Orchestration-utlösare stöder utdataparametrar samt indata. Returvärdet för funktionen används för att tilldela utdata-värdet och måste vara JSON-serialiserbar. Om en .net-funktion `Task` returnerar `void`eller, `null` kommer ett värde att sparas som utdata.

### <a name="trigger-sample"></a>Utlös exempel

Följande är ett exempel på hur den enklaste "Hello World" Orchestrator-funktionen kan se ut:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Objektet i Java Script representerar inte DurableOrchestrationContext, men [funktionen context som helhet.](../functions-reference-node.md#context-object) `context` Du kan komma åt Orchestration-metoder via `context` `df` objektets egenskap.

> [!NOTE]
> JavaScript-dirigering bör använda `return`. Biblioteket tar hand om att `context.done` anropa metoden. `durable-functions`

De flesta funktioner för att anropa aktiviteter i Orchestrator, så här är ett "Hello World"-exempel som visar hur du anropar en aktivitets funktion:

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

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Aktivitets utlösare

Med aktivitets utlösaren kan du redigera funktioner som anropas av Orchestrator functions.

Om du använder Visual Studio konfigureras aktivitets utlösaren med hjälp av attributet [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .net.

Om du använder vs Code eller Azure Portal för utveckling definieras aktivitets utlösaren av följande JSON-objekt i `bindings` matrisen med *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`är namnet på aktiviteten. Detta är det värde som används av Orchestrator Functions för att anropa den här aktivitets funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt denna Utlös ande bindning avsöker en kö i standard lagrings kontot för Function-appen. Den här kön är en intern implementerings information om tillägget, vilket är orsaken till att den inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende vid utlösare

Här följer några anmärkningar om aktivitets utlösaren:

* **Trådning** – till skillnad från Orchestration-utlösaren har aktivitets utlösare inga begränsningar kring trådning eller I/O. De kan behandlas som vanliga funktioner.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i aktivitets utlösare.
* **Meddelande synlighet** – meddelanden om aktivitets utlösare tas bort från kön och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -retur värden serialiseras till JSON och sparas i tabellen Orchestration-historik i Azure Table Storage.

> [!WARNING]
> Lagrings Server delen för aktivitets funktioner är en implementerings detalj och användar koden ska inte samverka med dessa lagrings enheter direkt.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Bindningen för aktivitets utlösare stöder både indata och utdata, precis som Orchestration-utlösaren. Här är några saker du behöver veta om indata och utdata:

* **indata** -.net-aktivitets funktioner använder internt [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som parameter typ. Alternativt kan en aktivitets funktion deklareras med valfri parameter typ som är JSON-serialiserbar. När du använder `DurableActivityContext`kan du anropa [\<GetInput T->](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) för att hämta och deserialisera aktivitetens funktions ingångar.
* **utdata** – aktivitets funktioner stöder utdataparametrar samt indata. Returvärdet för funktionen används för att tilldela utdata-värdet och måste vara JSON-serialiserbar. Om en .net-funktion `Task` returnerar `void`eller, `null` kommer ett värde att sparas som utdata.
* **metadata** – .net-aktivitets funktioner kan bindas `string instanceId` till en parameter för att hämta instans-ID: t för den överordnade dirigeringen.

### <a name="trigger-sample"></a>Utlös exempel

Följande är ett exempel på hur en enkel "Hello World"-aktivitets funktion kan se ut så här:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Standard parameter typen för .net `ActivityTriggerAttribute` -bindningen är. `DurableActivityContext` .NET-aktivitets utlösare stöder dock också bindning direkt till JSON-serializeable typer (inklusive primitiva typer), så att samma funktion kan för enklas på följande sätt:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-bindningar kan också skickas som ytterligare parametrar, så att samma funktion kan för enklas på följande sätt:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Skicka flera parametrar

Det går inte att skicka flera parametrar till en aktivitets funktion direkt. Rekommendationen i det här fallet är att skicka in en matris med objekt eller använda [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) -objekt i .net.

Följande exempel använder nya funktioner i [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) som lagts till med [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="orchestration-client"></a>Orchestration-klient

Genom att dirigera klient bindningen kan du skriva funktioner som interagerar med Orchestrator functions. Du kan till exempel agera på Orchestration-instanser på följande sätt:

* Starta dem.
* Fråga efter status.
* Avsluta dem.
* Skicka händelser till dem när de är igång.
* Rensa instans historik.

Om du använder Visual Studio kan du binda till Orchestration-klienten med hjälp av [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET-attributet.

Om du använder skript språk (t. ex. *CSX* -eller *. js* -filer) för utveckling definieras Orchestration-utlösaren av följande JSON- `bindings` objekt i matrisen för *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`– Används i scenarier där flera Functions-appar delar samma lagrings konto men måste isoleras från varandra. Om inget värde anges används standardvärdet `host.json` från. Värdet måste matcha det värde som används av målets Orchestrator-funktioner.
* `connectionName`– Namnet på en app-inställning som innehåller en anslutnings sträng för lagrings kontot. Det lagrings konto som representeras av den här anslutnings strängen måste vara samma som används av mål-Orchestrator-funktionerna. Om inget värde anges används standard anslutnings strängen för lagrings kontot för Function-appen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar dessa egenskaper och använder standard beteendet.

### <a name="client-usage"></a>Klient användning

I .net-funktioner binder du normalt till `DurableOrchestrationClient`, vilket ger dig fullständig åtkomst till alla klient-API: er som stöds av Durable functions. I Java Script visas samma API: er av det `DurableOrchestrationClient` objekt som returneras `getClient`från. API: er för klient objekt är:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (för närvarande endast .NET)

.Net-funktioner kan också bindas `IAsyncCollector<T>` till `T` WHERE [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) -StartOrchestrationArgs `JObject`eller.

Mer information om de här åtgärderna finns i [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-dokumentationen.

> [!WARNING]
> När du utvecklar lokalt i Java Script måste du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t. ex. `localhost:7071`använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i [GitHub-problemet](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Klient exempel (Visual Studio-utveckling)

Här är ett exempel på en Queue-utlöst funktion som startar en "HelloWorld"-dirigering.

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

### <a name="client-sample-not-visual-studio"></a>Klient exempel (inte Visual Studio)

Om du inte använder Visual Studio för utveckling kan du skapa följande *funktion. JSON* -fil. Det här exemplet visar hur du konfigurerar en köade funktion som använder den varaktiga dirigeringen av klient bindning:

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
      "direction": "in"
    }
  ]
}
```

Nedan visas språkspecifika exempel som startar nya Orchestrator Function-instanser.

#### <a name="c-sample"></a>C#-exempel

Följande exempel visar hur du använder den varaktiga Dirigerings klient bindningen för att starta en ny funktions instans C# från en skript funktion:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-exempel

Följande exempel visar hur du använder den varaktiga Dirigerings klient bindningen för att starta en ny funktions instans från en JavaScript-funktion:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Du hittar mer information om start instanser i [instans hantering](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-inställningar

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om kontroll punkter och repetitions beteenden](durable-functions-checkpointing-and-replay.md)