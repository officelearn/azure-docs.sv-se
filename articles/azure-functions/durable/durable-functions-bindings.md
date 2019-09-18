---
title: Bindningar för Durable Functions – Azure
description: Använda utlösare och bindningar för Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033588"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) -tillägget introducerar två nya Utlös ande bindningar som styr körningen av Orchestrator-och aktivitets funktioner. Den introducerar också en utgående bindning som fungerar som en klient för Durable Functions Runtime.

## <a name="orchestration-trigger"></a>Orchestration-utlösare

Genom Orchestration-utlösaren kan du skapa [robusta Orchestrator-funktioner](durable-functions-types-features-overview.md#orchestrator-functions). Den här utlösaren stöder start av nya Orchestrator Function-instanser och återupptar befintliga Orchestrator-funktioner som väntar på en aktivitet.

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
> Orchestrator-funktioner bör aldrig använda eventuella indata eller utgående bindningar än bindningen för Dirigerings utlösaren. Detta har möjlighet att orsaka problem med det varaktiga aktivitets tillägget eftersom dessa bindningar inte kan följa reglerna för enkel trådning och I/O. Om du vill använda andra bindningar lägger du till dem i en aktivitets funktion som anropas från din Orchestrator-funktion.

> [!WARNING]
> JavaScript Orchestrator-funktioner ska aldrig deklareras `async`.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Bindningen för Dirigerings utlösare stöder både indata och utdata. Här är några saker du behöver veta om indata och utdata:

* **indata** – .net-Orchestration-funktioner stöder endast [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) som parameter typ. Deserialisering av indata direkt i funktions signaturen stöds inte. Koden måste använda metoden [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) eller `getInput` (Java Script) för att hämta Orchestrator-funktionen indata. Dessa indata måste vara JSON-serialiserbara typer.
* **utdata** -Orchestration-utlösare stöder utdataparametrar samt indata. Returvärdet för funktionen används för att tilldela utdata-värdet och måste vara JSON-serialiserbar. Om en .net-funktion `Task` returnerar `void`eller, `null` kommer ett värde att sparas som utdata.

### <a name="trigger-sample"></a>Utlös exempel

Följande exempel kod visar vad den enklaste "Hello World" Orchestrator-funktionen kan se ut så här:

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

## <a name="activity-trigger"></a>Aktivitets utlösare

Med aktivitets utlösaren kan du redigera funktioner som anropas av Orchestrator functions, kallas [aktivitets funktioner](durable-functions-types-features-overview.md#activity-functions).

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

* `activity`är namnet på aktiviteten. Det här värdet är det namn som Orchestrator Functions använder för att anropa den här aktivitets funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

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

Följande exempel kod visar hur en enkel "Hello World"-aktivitets funktion kan se ut så här:

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


### <a name="using-input-and-output-bindings"></a>Använda indata och utgående bindningar

Du kan använda vanliga indata och utgående bindningar utöver bindningen för aktivitets utlösare. Du kan till exempel ta indata till din aktivitets bindning och skicka ett meddelande till en EventHub med hjälp av EventHub-utdata-bindningen:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestration-klient

Genom att dirigera klient bindningen kan du skriva funktioner som interagerar med Orchestrator functions. Dessa funktioner kallas ibland [klient funktioner](durable-functions-types-features-overview.md#client-functions). Du kan till exempel agera på Orchestration-instanser på följande sätt:

* Starta dem.
* Fråga efter status.
* Avsluta dem.
* Skicka händelser till dem när de är igång.
* Rensa instans historik.

Om du använder Visual Studio kan du binda till Orchestration-klienten med hjälp av [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET-attributet för Durable Functions 1,0. Från för hands versionen av Durable Functions 2,0 kan du binda till Orchestration-klienten med hjälp `DurableClientAttribute` av .NET-attributet.

Om du använder skript språk (till exempel *. CSX* -eller *. js* -filer) för utveckling, definieras Orchestration-utlösaren av följande JSON- `bindings` objekt i matrisen för *Function. JSON*:

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

I .net-funktioner binder du normalt till `DurableOrchestrationClient`, vilket ger dig fullständig åtkomst till alla klient-API: er som stöds av Durable functions. Från och med Durable Functions 2,0 binder du i stället till `IDurableOrchestrationClient` gränssnittet. I Java Script visas samma API: er av det objekt som returneras `getClient`från. API: er för klient objekt är:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

.Net-funktioner kan också bindas `IAsyncCollector<T>` till `T` WHERE [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) -StartOrchestrationArgs `JObject`eller.

Mer information om dessa åtgärder finns i [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-dokumentationen.

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

## <a name="entity-trigger"></a>Enhets utlösare

Med enhets utlösare kan du redigera [entitets funktioner](durable-functions-entities.md). Den här utlösaren stöder bearbetning av händelser för en angiven enhets instans.

När du använder Visual Studio-verktygen för Azure Functions konfigureras enhets utlösaren med `EntityTriggerAttribute` hjälp av .NET-attributet.

> [!NOTE]
> Enhets utlösare är tillgängliga i Durable Functions 2,0 och senare. Enhets utlösare är ännu inte tillgängliga för Java Script.

Internt denna Utlös ande bindning avsöker en serie med köer i standard lagrings kontot för Function-appen. Dessa köer är interna implementerings information om tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende vid utlösare

Här följer några anmärkningar om enhets utlösaren:

* **Enkel trådad**: En enskild dispatcher-tråd används för att bearbeta åtgärder för en viss entitet. Om flera meddelanden skickas till en enda entitet samtidigt bearbetas åtgärderna en i taget.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i enhets utlösare.
* **Meddelande synlighet** – meddelanden om enhets utlösare är i kö och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -enhets funktioner stöder inte retur värden. Det finns vissa API: er som kan användas för att spara tillstånd eller skicka tillbaka värden till dirigeringar.

Alla tillstånds ändringar som görs i en entitet under körningen sparas automatiskt när körningen har slutförts.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Varje entitets funktion har parameter typen `IDurableEntityContext`, som har följande medlemmar:

* **EntityName**: Hämtar namnet på entiteten som körs för tillfället.
* **EntityKey**: Hämtar nyckeln för den entitet som körs för tillfället.
* **EntityId**: Hämtar ID för entiteten som körs för tillfället.
* **OperationName**: hämtar namnet på den aktuella åtgärden.
* **IsNewlyConstructed**: returnerar `true` om entiteten inte fanns före åtgärden.
* **GetState\<TState > ()** : hämtar entitetens aktuella status. `TState` Parametern måste vara en primitiv eller JSON-serializeable typ.
* **SetState (objekt)** : uppdaterar enhetens status. `object` Parametern måste vara ett primitivt eller JSON-serializeable-objekt.
* **GetInput\<TInput > ()** : hämtar InInformationen för den aktuella åtgärden. `TInput` Typ parametern måste representera en primitiv eller JSON-serializeable typ.
* **Return (objekt)** : returnerar ett värde till den dirigering som anropade åtgärden. `object` Parametern måste vara ett primitivt eller JSON-serializeable-objekt.
* **DestructOnExit ()** : tar bort entiteten när den aktuella åtgärden har slutförts.
* **SignalEntity (EntityId, sträng, objekt)** : skickar ett envägs meddelande till en entitet. `object` Parametern måste vara ett primitivt eller JSON-serializeable-objekt.

När du använder det klassbaserade enhets programmerings läget `IDurableEntityContext` kan du referera till objektet med hjälp `Entity.Current` av egenskapen tråd-static.

### <a name="trigger-sample---entity-function"></a>Funktionen utlösnings exempel-entitet

Följande kod är ett exempel på en enkel *Counter* -entitet som implementeras som en standard funktion. Den här funktionen definierartre åtgärder `add`, `reset`, och `get`, som körs på ett heltals tillstånds värde `currentValue`.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Utlös exempel på enhets klass

Följande exempel är en motsvarande implementering av den tidigare `Counter` entiteten med hjälp av .NET-klasser och-metoder.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Funktionens start punkt metod med `[FunctionName]` attributet *måste* deklareras `static` när du använder entitets klasser. Metoder som inte är statiska kan leda till att flera objekt initieras och potentiellt andra odefinierade beteenden.

Enhets klasser har särskilda metoder för att interagera med bindningar och .NET-beroende inmatning. Mer information finns i artikeln [beständiga entiteter](durable-functions-entities.md) .

## <a name="entity-client"></a>Enhets klient

Med enhets klientens bindning kan du utlösa [enhets funktioner](#entity-trigger)asynkront. Dessa funktioner kallas ibland [klient funktioner](durable-functions-types-features-overview.md#client-functions).

Om du använder Visual Studio kan du binda till enhets klienten med hjälp `DurableClientAttribute` av .NET-attributet.

> [!NOTE]
> Kan också användas för att binda till Orchestration- [klienten.](#orchestration-client) `[DurableClientAttribute]`

Om du använder skript språk (till exempel *. CSX* -eller *. js* -filer) för utveckling, definieras enhets utlösaren av följande JSON- `bindings` objekt i matrisen med *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`– Används i scenarier där flera Functions-appar delar samma lagrings konto men måste isoleras från varandra. Om inget värde anges används standardvärdet `host.json` från. Värdet måste matcha det värde som används av entitetens mål.
* `connectionName`– Namnet på en app-inställning som innehåller en anslutnings sträng för lagrings kontot. Det lagrings konto som representeras av den här anslutnings strängen måste vara samma som används av entiteten för målentiteten. Om inget värde anges används standard anslutnings strängen för lagrings kontot för Function-appen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar de valfria egenskaperna och använder standard beteendet.

### <a name="entity-client-usage"></a>Användning av enhets klient

I .net-funktioner binder du normalt till `IDurableEntityClient`, vilket ger dig fullständig åtkomst till alla klient-API: er som stöds av varaktiga enheter. Du kan också binda till `IDurableClient` gränssnittet, som ger åtkomst till klient-API: er för både entiteter och dirigeringar. API: er för klient objekt är:

* **ReadEntityStateAsync\<T >** : läser status för en entitet.
* **SignalEntityAsync**: skickar ett envägs meddelande till en entitet och väntar på att det ska placeras i kö.
* **SignalEntityAsync\<TEntityInterface >** : samma som `SignalEntityAsync` , men använder ett genererat proxyobjekt av `TEntityInterface`typen.
* **CreateEntityProxy\<TEntityInterface >** : genererar dynamiskt en dynamisk proxy av typen `TEntityInterface` för att skapa typ säkra anrop till entiteter.

> [!NOTE]
> Det är viktigt att förstå att föregående "signal-åtgärder" är asynkrona. Det går inte att anropa en entitets funktion och få tillbaka ett retur värde från en klient. På samma sätt kan `SignalEntityAsync` det hända att den returneras innan enheten börjar köra åtgärden. Endast Orchestrator-funktioner kan anropa enhets funktioner synkront och bearbeta retur värden.

API: erna kräver att du anger den unika identifieraren för entiteten som en `EntityId`. `SignalEntityAsync` Dessa API: er kan också ta med namnet på enhets åtgärden som `string` en och nytto lasten för åtgärden som en JSON- `object`serializeable. Om målentiteten inte finns skapas den automatiskt med angivet entitets-ID.

### <a name="client-sample-untyped"></a>Klient exempel (inte avskrivit)

Här är ett exempel på en kö – utlöst funktion som anropar en "Counter"-entitet.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Klient exempel (skrivet)

Det är möjligt att generera ett proxyobjekt för typ säker åtkomst till enhets åtgärder. Om du vill generera en typ säker proxy måste entitetstypen implementera ett gränssnitt. Anta till exempel att `Counter` entiteten ovan implementerade ett `ICounter` gränssnitt, som definieras enligt följande:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klient koden kan sedan använda `SignalEntityAsync<TEntityInterface>` och `ICounter` ange gränssnittet som typ parameter för att generera en typ säker proxy. Användningen av typ säkra proxyservrar visas i följande kod exempel:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

I föregående exempel `proxy` är parametern en dynamiskt genererad instans av `ICounter`, som internt översätter anropet till `Add` till motsvarande (inte avskrivit) anrop till `SignalEntityAsync`.

Det finns några regler för att definiera enhets gränssnitt:

* Typ parametern `TEntityInterface` i `SignalEntityAsync<TEntityInterface>` måste vara ett gränssnitt.
* Enhets gränssnitt får bara definiera metoder.
* Enhets gränssnitts metoder får inte definiera mer än en parameter.
* Entitets gränssnitts metoder `void`måste `Task`returnera, `Task<T>` eller `T` där är ett retur värde.
* Enhets gränssnitt måste ha exakt en konkret implementerings klass inom samma sammansättning (det vill säga enhets klassen).

Om någon av dessa regler överskrids utlöses en `InvalidOperationException` körning vid körning. I undantags meddelandet förklaras vilken regel som har brutits.

> [!NOTE]
> `SignalEntityAsync` API: erna utgör enkelriktade åtgärder. Om ett enhets gränssnitt `Task<T>`returnerar, är värdet `T` för parametern alltid null eller `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-inställningar

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inbyggd HTTP API-referens för instans hantering](durable-functions-http-api.md)