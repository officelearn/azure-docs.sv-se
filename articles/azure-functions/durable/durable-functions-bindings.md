---
title: Bindningar för varaktiga funktioner - Azure
description: Så här använder du utlösare och bindningar för tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278225"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för varaktiga funktioner (Azure-funktioner)

Tillägget [Varaktiga funktioner](durable-functions-overview.md) introducerar två nya utlösarbindningar som styr körningen av orchestrator- och aktivitetsfunktioner. Den introducerar också en utdatabindning som fungerar som en klient för körningen varaktiga funktioner.

## <a name="orchestration-trigger"></a>Utlösare av orkestrering

Orchestration-utlösaren gör att du kan skapa [varaktiga orchestrator-funktioner](durable-functions-types-features-overview.md#orchestrator-functions). Den här utlösaren stöder start av nya orchestrator-funktionsinstanser och återuppta befintliga orchestrator-funktionsinstanser som "väntar" på en aktivitet.

När du använder Visual Studio-verktygen för Azure Functions konfigureras orchestration-utlösaren med attributet [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET.

När du skriver orchestrator-funktioner i skriptspråk (till exempel JavaScript- eller C#-skript) definieras orchestration-utlösaren av följande JSON-objekt i matrisen `bindings` för filen *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`är namnet på den orkestrering som klienter måste använda när de vill starta nya instanser av den här orchestrator-funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt avsöker den här utlösaren bindning en serie köer i standardlagringskontot för funktionsappen. Dessa köer är intern implementeringsinformation för tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindningsegenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här är några anteckningar om orchestration utlösa:

* **Entrådning** - En enda sändartråd används för alla orchestrator-funktionskörning på en enda värdinstans. Därför är det viktigt att se till att orchestrator funktionskoden är effektiv och inte utför någon I / O. Det är också viktigt att se till att den här tråden inte utför något asynkron arbete förutom när du väntar på varaktiga funktioner-specifika uppgiftstyper.
* **Hantering av giftmeddelanden** - Det finns inget stöd för giftmeddelande i orchestration-utlösare.
* **Meddelandesynlighet** - Orchestration-utlösarmeddelanden är avqueuerade och hålls osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge funktionsappen körs och är felfri.
* **Returvärden** - Returvärden serialiseras till JSON och sparas i orchestration history-tabellen i Azure Table storage. Dessa returvärden kan efterfrågas av orchestration-klientbindningen, som beskrivs senare.

> [!WARNING]
> Orchestrator-funktioner får aldrig använda andra indata- eller utdatabindningar än orkesterreriggsbindningen. Om du gör det kan det orsaka problem med tillägget Varaktig aktivitet eftersom dessa bindningar kanske inte följer reglerna med en tråd och I/O. Om du vill använda andra bindningar lägger du till dem i en aktivitetsfunktion som anropas från orchestrator-funktionen.

> [!WARNING]
> JavaScript orchestrator funktioner bör `async`aldrig deklareras .

### <a name="trigger-usage-net"></a>Utlösare användning (.NET)

Den orchestration utlösande bindningen stöder både ingångar och utgångar. Här är några saker att veta om indata och utdatahantering:

* **indata** - .NET orchestration `DurableOrchestrationContext` funktioner stöder endast som en parametertyp. Avserialisering av indata direkt i funktionssignaturen stöds inte. Koden måste `GetInput<T>` använda metoden (.NET) eller `getInput` (JavaScript) för att hämta orchestrator-funktionsindata. Dessa indata måste vara JSON-serialiserbara typer.
* **utdata** - Orchestration utlöser stöder utdatavärden och indata. Funktionens returvärde används för att tilldela utdatavärdet och måste vara JSON-serialiserbara. Om en .NET-funktion returneras `Task` eller `void`sparas ett `null` värde som utdata.

### <a name="trigger-sample"></a>Exempel på utlösare

Följande exempelkod visar hur den enklaste orchestratorfunktionen "Hello World" kan se ut:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Varaktiga funktioner.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Objektet `context` i JavaScript representerar inte DurableOrchestrationContext, men [funktionskontexten som helhet](../functions-reference-node.md#context-object). Du kan komma åt `context` orkestreringsmetoder via objektets `df` egenskap.

> [!NOTE]
> JavaScript orchestrators `return`bör använda . Biblioteket `durable-functions` tar hand om `context.done` att anropa metoden.

De flesta orchestrator funktioner samtal aktivitetsfunktioner, så här är en "Hello World" exempel som visar hur man ringer en aktivitetsfunktion:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Aktivitetsutlösare

Med aktivitetsutlösaren kan du skapa funktioner som anropas av orchestrator-funktioner, så kallade [aktivitetsfunktioner](durable-functions-types-features-overview.md#activity-functions).

Om du använder Visual Studio konfigureras aktivitetsutlösaren med `ActivityTriggerAttribute` attributet .NET.

Om du använder VS-kod eller Azure-portalen för utveckling definieras aktivitetsutlösaren av följande JSON-objekt i matrisen `bindings` *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`är namnet på aktiviteten. Det här värdet är namnet som orchestrator-funktioner använder för att anropa den här aktivitetsfunktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt avsöker den här utlösaren en kö i funktionsappens standardlagringskonto. Den här kön är en intern implementeringsdetalj i tillägget, vilket är anledningen till att den inte uttryckligen konfigureras i bindningsegenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här är några anteckningar om aktivitetsutlösaren:

* **Trådning** - Till skillnad från orchestration-utlösaren har aktivitetsutlösare inga begränsningar kring trådning eller I/O. De kan behandlas som vanliga funktioner.
* **Hantering av giftmeddelanden** - Det finns inget stöd för giftmeddelande i aktivitetsutlösare.
* **Meddelandesynlighet** - Aktivitetsutlösare meddelanden är avqueuerade och hålls osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge funktionsappen körs och är felfri.
* **Returvärden** - Returvärden serialiseras till JSON och sparas i orchestration history-tabellen i Azure Table storage.

> [!WARNING]
> Lagringsgränssnittet för aktivitetsfunktioner är en implementeringsdetalj och användarkoden bör inte interagera med dessa lagringsenheter direkt.

### <a name="trigger-usage-net"></a>Utlösare användning (.NET)

Aktivitetsutlösarens bindning stöder både indata och utdata, precis som orchestration-utlösaren. Här är några saker att veta om indata och utdatahantering:

* **indata** - .NET-aktivitetsfunktioner används `DurableActivityContext` internt som parametertyp. Alternativt kan en aktivitetsfunktion deklareras med valfri parametertyp som är JSON-serialiserbar. När du `DurableActivityContext`använder kan `GetInput<T>` du ringa för att hämta och avserialisera indata för aktivitetsfunktionen.
* **utdata - Aktivitetsfunktioner** stöder utdatavärden och ingångar. Funktionens returvärde används för att tilldela utdatavärdet och måste vara JSON-serialiserbara. Om en .NET-funktion returneras `Task` eller `void`sparas ett `null` värde som utdata.
* **metadata** - .NET-aktivitetsfunktioner `string instanceId` kan binda till en parameter för att hämta instans-ID:t för den överordnade orkestreringen.

### <a name="trigger-sample"></a>Exempel på utlösare

Följande exempelkod visar hur en enkel "Hello World"-aktivitetsfunktion kan se ut:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableActivityContext` måste `IDurableActivityContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Varaktiga funktioner.](durable-functions-versions.md)

Standardparametertypen för `ActivityTriggerAttribute` .NET-bindningen är `IDurableActivityContext`. .NET-aktivitetsutlösare stöder dock också bindning direkt till JSON-serialiserabara typer (inklusive primitiva typer), så samma funktion kan förenklas enligt följande:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (endast funktioner 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-bindningar kan också skickas in som ytterligare parametrar, så samma funktion kan förenklas på följande sätt:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Använda ingångs- och utdatabindningar

Du kan använda vanliga in- och utdatabindningar utöver aktivitetsutlösningsbindningen. Du kan till exempel ta indata till din aktivitetsbindning och skicka ett meddelande till en EventHub med eventhub-utdatabindningen:

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

## <a name="orchestration-client"></a>Orkestreringsklient

Med orkestreringsklientbindningen kan du skriva funktioner som interagerar med orchestrator-funktioner. Dessa funktioner kallas ibland [klientfunktioner](durable-functions-types-features-overview.md#client-functions). Du kan till exempel agera på orchestration-instanser på följande sätt:

* Starta dem.
* Fråga deras status.
* Avsluta dem.
* Skicka händelser till dem medan de körs.
* Rensa instanshistorik.

Om du använder Visual Studio kan du binda till orchestration-klienten `OrchestrationClientAttribute` med attributet .NET för varaktiga funktioner 1.0. Med början i durable functions 2.0 kan du binda `DurableClientAttribute` till orchestration-klienten med attributet .NET.

Om du använder skriptspråk (till exempel *CSX-* eller *.js-filer)* för utveckling definieras orchestration-utlösaren `bindings` av följande JSON-objekt i matrisen *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Används i scenarier där flera funktionsappar delar samma lagringskonto men måste isoleras från varandra. Om inget anges används standardvärdet från. `host.json` Det här värdet måste matcha värdet som används av målorkesoratorfunktionerna.
* `connectionName`- Namnet på en appinställning som innehåller en anslutningssträng för lagringskonto. Lagringskontot som representeras av den här anslutningssträngen måste vara samma som används av målorkesoratorfunktionerna. Om inget anges används standardanslutningssträngen för lagringskonto för funktionsappen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar dessa egenskaper och förlitar dig på standardbeteendet.

### <a name="client-usage"></a>Användning av klient

I .NET-funktioner binder `IDurableOrchestrationClient`du vanligtvis till , vilket ger dig full åtkomst till alla orchestration-klient-API:er som stöds av varaktiga funktioner. I de äldre varaktiga funktionerna 2.x-utgåvorna binder du i stället till `DurableOrchestrationClient` klassen. I JavaScript exponeras samma API:er av `getClient`objektet som returneras från . API:er på klientobjektet inkluderar:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativt kan .NET-funktioner `IAsyncCollector<T>` binda `T` `StartOrchestrationArgs` till `JObject`var eller .

Mer information om dessa åtgärder `IDurableOrchestrationClient` finns i API-dokumentationen.

### <a name="client-sample-visual-studio-development"></a>Exempel på klient (Utveckling av Visual Studio)

Här är ett exempel kö-utlöst funktion som startar en "HelloWorld" orkestrering.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Den tidigare C#-koden är avsedd för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `OrchestrationClient` måste du `DurableClient` använda attributet i `DurableOrchestrationClient` stället för `IDurableOrchestrationClient`attributet och du måste använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Varaktiga funktioner.](durable-functions-versions.md)

### <a name="client-sample-not-visual-studio"></a>Klientexempel (inte Visual Studio)

Om du inte använder Visual Studio för utveckling kan du skapa följande *function.json-fil.* I det här exemplet visas hur du konfigurerar en köutlöst funktion som använder den varaktiga orchestration-klientbindningen:

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Den tidigare JSON är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `orchestrationClient` måste `durableClient` du använda i stället för som utlösartyp. Mer information om skillnaderna mellan versioner finns i artikeln [Varaktiga funktioner.](durable-functions-versions.md)

Följande är språkspecifika exempel som startar nya orchestrator-funktionsinstanser.

#### <a name="c-script-sample"></a>Exempel på C#-skript

Följande exempel visar hur du använder den varaktiga orchestration-klientbindningen för att starta en ny funktionsinstans från en köutlöst C#-funktion:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Den tidigare koden är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationClient` måste du `IDurableOrchestrationClient`använda parametertypen i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Varaktiga funktioner.](durable-functions-versions.md)

#### <a name="javascript-sample"></a>JavaScript-exempel

Följande exempel visar hur du använder den varaktiga orchestration-klientbindningen för att starta en ny funktionsinstans från en JavaScript-funktion:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mer information om startinstanser finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Utlösare för entitet

Med entitetsutlösare kan du skapa [entitetsfunktioner](durable-functions-entities.md). Den här utlösaren stöder bearbetning av händelser för en viss entitetsinstans.

När du använder Visual Studio-verktygen för Azure Functions `EntityTriggerAttribute` konfigureras entitetsutlösaren med attributet .NET.

> [!NOTE]
> Entitetsutlösare är tillgängliga från varaktiga funktioner 2.x.

Internt avsöker den här utlösaren bindning en serie köer i standardlagringskontot för funktionsappen. Dessa köer är intern implementeringsinformation för tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindningsegenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här är några anteckningar om utlösaren för entiteten:

* **Entrådad:** En enda avsändare tråd används för att bearbeta operationer för en viss entitet. Om flera meddelanden skickas till en enda entitet samtidigt bearbetas åtgärderna en i taget.
* **Hantering av giftmeddelanden** - Det finns inget stöd för giftmeddelande i entitetsutlösare.
* **Meddelandesynlighet** - Entitetsutlösare meddelanden är avqueuerade och hålls osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge funktionsappen körs och är felfri.
* **Returvärden** - Entitetsfunktioner stöder inte returvärden. Det finns specifika API:er som kan användas för att spara tillstånd eller skicka tillbaka värden till orkestreringar.

Alla tillståndsändringar som görs i en entitet under körningen sparas automatiskt efter att körningen har slutförts.

### <a name="trigger-usage-net"></a>Utlösare användning (.NET)

Varje entitetsfunktion `IDurableEntityContext`har en parametertyp av , som har följande medlemmar:

* **EntityName**: namnet på den enhet som körs för närvarande.
* **EntityKey**: nyckeln till den enhet som körs för närvarande.
* **EntityId**: ID för den enhet som körs för närvarande.
* **OperationName**: namnet på den aktuella åtgärden.
* **HasState**: om entiteten finns, det vill säga har något tillstånd. 
* **GetState\<TState>()**: hämtar det aktuella tillståndet för entiteten. Om den inte redan finns skapas den `default<TState>`och initieras till . Parametern `TState` måste vara en primitiv eller JSON-serialiserabar typ. 
* **GetState\<TState>(initfunction)**: hämtar det aktuella tillståndet för entiteten. Om den inte redan finns skapas den `initfunction` genom att anropa den angivna parametern. Parametern `TState` måste vara en primitiv eller JSON-serialiserabar typ. 
* **SetState(arg):** skapar eller uppdaterar tillståndet för entiteten. Parametern `arg` måste vara ett JSON-serializeable objekt eller primitiv.
* **DeleteState()**: tar bort tillståndet för entiteten. 
* **GetInput\<TInput>()**: hämtar indata för den aktuella åtgärden. Typparametern `TInput` måste vara en primitiv eller JSON-serialiserabar typ.
* **Return(arg)**: returnerar ett värde till den orkestrering som anropade åtgärden. Parametern `arg` måste vara ett primitivt eller JSON-serialifierbart objekt.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input):** skickar ett enkelriktat meddelande till en entitet. Parametern `operation` måste vara en icke-null-sträng, det valfria `scheduledTimeUtc` måste vara en UTC-datetime då åtgärden ska anropas och `input` parametern måste vara ett primitivt eller JSON-serialifierbart objekt.
* **CreateNewOrchestration(orchestratorFunctionName, input)**: startar en ny orkestrering. Parametern `input` måste vara ett primitivt eller JSON-serialifierbart objekt.

Objektet `IDurableEntityContext` som skickas till entitetsfunktionen `Entity.Current` kan nås med egenskapen async-local. Den här metoden är praktisk när du använder den klassbaserade programmeringsmodellen.

### <a name="trigger-sample-c-function-based-syntax"></a>Utlösarexempel (C#funktionsbaserad syntax)

Följande kod är ett exempel *på* en enkel counter-entitet som implementeras som en varaktig funktion. Den här funktionen definierar `reset`tre `get`operationer, `add`, och , som var och en fungerar på ett heltalstillstånd.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Mer information om den funktionsbaserade syntaxen och hur du använder den finns i [Funktionsbaserad syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Utlösarexempel (klassbaserad syntax för C#)

Följande exempel är en motsvarande `Counter` implementering av entiteten med hjälp av klasser och metoder.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Tillståndet för den här entiteten är ett objekt av typen `Counter`, som innehåller ett fält som lagrar det aktuella värdet för räknaren. Om du vill behålla objektet i lagringen serialiseras [Json.NET](https://www.newtonsoft.com/json) det och avserialiseras det av Json.NET-biblioteket. 

Mer information om den klassbaserade syntaxen och hur du använder den finns i [Definiera entitetsklasser](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Funktionsstartpunktsmetoden `[FunctionName]` med attributet `static` *måste* deklareras när entitetsklasser används. Icke-statiska startpunktsmetoder kan resultera i flera objektinitisering och potentiellt andra odefinierade beteenden.

Entitetsklasser har särskilda mekanismer för att interagera med bindningar och .NET-beroendeinjektion. Mer information finns i [Entitetskonstruktion](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Exempel på utlösare (JavaScript)

Följande kod är ett exempel på en enkel *counter-enhet* som implementeras som en varaktig funktion skriven i JavaScript. Den här funktionen definierar `reset`tre `get`operationer, `add`, och , som var och en fungerar på ett heltalstillstånd.

**funktion.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Varaktiga entiteter är tillgängliga i JavaScript som börjar `durable-functions` med version **1.3.0** av npm-paketet.

## <a name="entity-client"></a>Entitetsklient

Med entitetsklientbindningen kan du asynkront utlösa [entitetsfunktioner](#entity-trigger). Dessa funktioner kallas ibland [klientfunktioner](durable-functions-types-features-overview.md#client-functions).

Om du använder Visual Studio kan du binda till `DurableClientAttribute` entitetsklienten med attributet .NET.

> [!NOTE]
> Kan `[DurableClientAttribute]` också användas för att binda till [orchestration-klienten](#orchestration-client).

Om du använder skriptspråk (till exempel *CSX-* eller *.js-filer)* för utveckling, definieras entitetsutlösaren av följande JSON-objekt i matrisen `bindings` *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Används i scenarier där flera funktionsappar delar samma lagringskonto men måste isoleras från varandra. Om inget anges används standardvärdet från. `host.json` Det här värdet måste matcha det värde som används av målentitetsfunktionerna.
* `connectionName`- Namnet på en appinställning som innehåller en anslutningssträng för lagringskonto. Lagringskontot som representeras av den här anslutningssträngen måste vara det samma som används av målentitetsfunktionerna. Om inget anges används standardanslutningssträngen för lagringskonto för funktionsappen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar de valfria egenskaperna och förlitar dig på standardbeteendet.

### <a name="entity-client-usage"></a>Användning av entitetsklient

I .NET-funktioner binder `IDurableEntityClient`du vanligtvis till , vilket ger dig full åtkomst till alla klient-API:er som stöds av varaktiga entiteter. Du kan också `IDurableOrchestrationClient` binda till gränssnittet, som ger åtkomst till klient-API:er för både entiteter och orkestreringar. API:er på klientobjektet inkluderar:

* **ReadEntityStateAsync\<T>**: läser tillståndet för en entitet. Den returnerar ett svar som anger om målentiteten finns, och i så fall vad dess tillstånd är.
* **SignalEntityAsync**: skickar ett enkelriktat meddelande till en entitet och väntar på att den ska köas.
* **Lista Entiteter:** frågor om tillståndet för flera entiteter. Entiteter kan efterfrågas efter *namn* och *sista åtgärdstid*.

Det finns ingen anledning att skapa målentiteten innan du skickar en signal - entitetstillståndet kan skapas inifrån entitetsfunktionen som hanterar signalen.

> [!NOTE]
> Det är viktigt att förstå att "signaler" som skickas från klienten helt enkelt är enqueued, att behandlas asynkront vid ett senare tillfälle. I synnerhet `SignalEntityAsync` returnerar vanligtvis returen innan entiteten ens startar operationen, och det är inte möjligt att få tillbaka returvärdet eller observera undantag. Om starkare garantier krävs (t.ex. för arbetsflöden) bör *orchestrator-funktioner* användas, som kan vänta på att entitetsåtgärderna slutförs och kan bearbeta returvärden och observera undantag.

### <a name="example-client-signals-entity-directly---c"></a>Exempel: klientsignalerhet direkt - C #

Här är ett exempel kö-utlöst funktion som anropar en "Counter" entitet.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Exempel: klientsignaler enhet via gränssnitt - C #

Om möjligt rekommenderar vi [åtkomst till entiteter via gränssnitt](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) eftersom det ger mer typkontroll. Anta till exempel `Counter` att den enhet `ICounter` som nämns tidigare implementerade ett gränssnitt, definierat på följande sätt:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klientkoden kan `SignalEntityAsync<ICounter>` sedan användas för att generera en typsäker proxy:

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

Parametern `proxy` är en dynamiskt `ICounter`genererad instans av , `Add` som internt översätter anropet till motsvarande (otypade) anrop till `SignalEntityAsync`.

> [!NOTE]
> API:erna `SignalEntityAsync` representerar enkelriktade åtgärder. Om ett entitetsgränssnitt returnerar `Task<T>`kommer `T` parameterns `default`värde alltid att vara null eller .

I synnerhet är det inte meningsfullt `Get` att signalera operationen, eftersom inget värde returneras. Klienter kan i `ReadStateAsync` stället använda antingen för att komma åt räknarläget `Get` direkt eller starta en orchestrator-funktion som anropar åtgärden.

### <a name="example-client-signals-entity---javascript"></a>Exempel: klientsignalerhet - JavaScript

Här är ett exempel kö-utlöst funktion som signalerar en "Counter" entitet i JavaScript.

**funktion.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Varaktiga entiteter är tillgängliga i JavaScript som börjar `durable-functions` med version **1.3.0** av npm-paketet.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>värd.json-inställningar

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inbyggd HTTP API-referens för instanshantering](durable-functions-http-api.md)
