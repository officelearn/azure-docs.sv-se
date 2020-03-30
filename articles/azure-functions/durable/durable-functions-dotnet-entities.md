---
title: Utvecklarguide till varaktiga entiteter i .NET - Azure-funktioner
description: Så här arbetar du med varaktiga entiteter i .NET med tillägget Varaktiga funktioner för Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278134"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Utvecklarguide till varaktiga enheter i .NET

I den här artikeln beskriver vi tillgängliga gränssnitt för att utveckla varaktiga enheter med .NET i detalj, inklusive exempel och allmänna råd. 

Entitetsfunktioner ger programutvecklare med serverlösa program på ett bekvämt sätt att organisera programtillstånd som en samling finkorniga entiteter. Mer information om de underliggande begreppen finns i artikeln [Varaktiga entitetER: Begrepp.](durable-functions-entities.md)

Vi erbjuder för närvarande två API:er för att definiera entiteter:

- Den **klassbaserade syntaxen** representerar entiteter och operationer som klasser och metoder. Den här syntaxen producerar lättläst kod och gör att åtgärder kan anropas på ett typkontrollerat sätt via gränssnitt. 

- Den **funktionsbaserade syntaxen** är ett gränssnitt på lägre nivå som representerar entiteter som funktioner. Det ger exakt kontroll över hur entitetsåtgärderna skickas och hur entitetstillståndet hanteras.  

Den här artikeln fokuserar främst på den klassbaserade syntaxen, eftersom vi förväntar oss att den passar bättre för de flesta program. Den [funktionsbaserade syntaxen](#function-based-syntax) kan dock vara lämplig för program som vill definiera eller hantera sina egna abstraktioner för entitetstillstånd och operationer. Det kan också vara lämpligt att implementera bibliotek som kräver generiskhet som för närvarande inte stöds av den klassbaserade syntaxen. 

> [!NOTE]
> Den klassbaserade syntaxen är bara ett lager ovanpå den funktionsbaserade syntaxen, så båda varianterna kan användas omväxlande i samma program. 
 
## <a name="defining-entity-classes"></a>Definiera entitetsklasser

Följande exempel är en `Counter` implementering av en entitet som lagrar ett `Add` `Reset`enda `Get`värde `Delete`av typ heltal och erbjuder fyra operationer , , och .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Funktionen `Run` innehåller den standard som krävs för att använda den klassbaserade syntaxen. Det måste vara en *statisk* Azure-funktion. Den körs en gång för varje åtgärdsmeddelande som bearbetas av entiteten. När `DispatchAsync<T>` anropas och entiteten inte redan finns i minnet, konstruerar den ett objekt av typen `T` och fyller i dess fält från den senast bevarade JSON som hittades i lagring (om någon). Sedan anropas metoden med det matchande namnet.

> [!NOTE]
> Tillståndet för en klassbaserad **entitet skapas implicit innan entiteten** bearbetar en `Entity.Current.DeleteState()`operation och kan tas bort **explicit** i en åtgärd genom att anropa .

### <a name="class-requirements"></a>Klasskrav
 
Entitetsklasser är POCOs (vanliga gamla CLR-objekt) som inte kräver några speciella superklasser, gränssnitt eller attribut. Emellertid:

- Klassen måste vara konstruktibel (se [Entitetskonstruktion](#entity-construction)).
- Klassen måste vara JSON-serialisable (se [Entitetsserier](#entity-serialization)).

Alla metoder som är avsedda att anropas som en åtgärd måste också uppfylla ytterligare krav:

- En åtgärd måste ha högst ett argument och får inte ha några överbelastningar eller allmänna typargument.
- En åtgärd som är avsedd att anropas `Task` från `Task<T>`en orkestrering med hjälp av ett gränssnitt måste returneras eller .
- Argument och returvärden måste vara serialiserbara värden eller objekt.

### <a name="what-can-operations-do"></a>Vad kan operationer göra?

Alla entitetsåtgärder kan läsa och uppdatera entitetstillståndet och ändringar i tillståndet sparas automatiskt i lagring. Dessutom kan åtgärder utföra extern I/O eller andra beräkningar, inom de allmänna gränser som är gemensamma för alla Azure-funktioner.

Verksamheten har också tillgång till `Entity.Current` funktioner som tillhandahålls av sammanhanget:

* `EntityName`: Namnet på den enhet som körs för närvarande.
* `EntityKey`: nyckeln till den enhet som för närvarande körs.
* `EntityId`: ID för den enhet som körs för närvarande (inkluderar namn och nyckel).
* `SignalEntity`: skickar ett enkelriktad meddelande till en entitet.
* `CreateNewOrchestration`: startar en ny orkestrering.
* `DeleteState`: tar bort tillståndet för den här entiteten.

Vi kan till exempel ändra räknarentiteten så att den startar en orkestrering när räknaren når 100 och skickar entitets-ID:t som ett indataargument:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Komma åt enheter direkt

Klassbaserade entiteter kan nås direkt med hjälp av explicita strängnamn för entiteten och dess operationer. Vi ger några exempel nedan; för en djupare förklaring av de underliggande begreppen (t.ex. signaler kontra anrop) se diskussionen i [Access-entiteter](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Om möjligt rekommenderar vi [åtkomst till entiteter via gränssnitt,](#accessing-entities-through-interfaces)eftersom det ger fler typkontroll.

### <a name="example-client-signals-entity"></a>Exempel: klientsignalerentitet

Följande Azure Http-funktion implementerar en DELETE-åtgärd med REST-konventioner. Den skickar en borttagningssignal till den mottitet vars nyckel skickas i URL-sökvägen.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Exempel: klienten läser entitetstillstånd

Följande Azure Http-funktion implementerar en GET-åtgärd med REST-konventioner. Den läser det aktuella tillståndet för den mottitet vars nyckel skickas i URL-sökvägen.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Objektet som returneras av `ReadEntityStateAsync` är bara en lokal kopia, det vill säga en ögonblicksbild av entitetstillståndet från någon tidigare tidpunkt. I synnerhet kan det vara inaktuellt, och att ändra det här objektet har ingen effekt på den faktiska entiteten. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exempel: orkestrering signalerar först, sedan anropar entiteten

Följande orkestrering signalerar en mottitet att öka den och anropar sedan samma entitet för att läsa dess senaste värde.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Komma åt entiteter via gränssnitt

Gränssnitt kan användas för åtkomst till entiteter via genererade proxyobjekt. Den här metoden säkerställer att namn och argumenttyp för en operation matchar vad som implementeras. Vi rekommenderar att du använder gränssnitt för åtkomst till entiteter när det är möjligt.

Vi kan till exempel ändra motexemplet på följande sätt:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Entity klasser och enhet gränssnitt liknar korn och korn gränssnitt populariserades av [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Mer information om likheter och skillnader mellan varaktiga entiteter och Orleans finns i [Jämförelse med virtuella aktörer](durable-functions-entities.md#comparison-with-virtual-actors).

Förutom att tillhandahålla typkontroll, gränssnitt är användbara för en bättre separation av oro inom programmet. Eftersom en entitet till exempel kan implementera flera gränssnitt kan en enda entitet hantera flera roller. Eftersom ett gränssnitt kan implementeras av flera entiteter kan allmänna kommunikationsmönster implementeras som återanvändbara bibliotek.

### <a name="example-client-signals-entity-through-interface"></a>Exempel: klientsignaler enhet genom gränssnitt

Klientkod kan `SignalEntityAsync<TEntityInterface>` användas för att skicka `TEntityInterface`signaler till entiteter som implementerar . Ett exempel:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

I det här `proxy` exemplet är parametern `ICounter`en dynamiskt genererad förekomst av , som internt översätter anropet till `Delete` till en signal.

> [!NOTE]
> API:erna `SignalEntityAsync` kan endast användas för enkelriktade åtgärder. Även om en `Task<T>`operation returnerar `T` kommer parameterns `default`värde alltid att vara null eller , inte det faktiska resultatet.
Det är till exempel inte meningsfullt `Get` att signalera åtgärden, eftersom inget värde returneras. Klienter kan i `ReadStateAsync` stället använda antingen för att komma åt räknarläget `Get` direkt eller starta en orchestrator-funktion som anropar åtgärden. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exempel: orkestrering signalerar först, sedan anropar entiteten via proxy

För att anropa eller signalera en `CreateEntityProxy` entitet inifrån en orkestrering, kan användas, tillsammans med gränssnittstypen, för att generera en proxy för entiteten. Denna proxy kan sedan användas för att anropa eller signalera åtgärder:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicit signaleras alla `void` åtgärder som returneras och `Task` alla `Task<T>` åtgärder som returneras eller anropas. Man kan ändra detta standardbeteende och signalåtgärder även om `SignalEntity<IInterfaceType>` de returnerar aktivitet genom att använda metoden explicit.

### <a name="shorter-option-for-specifying-the-target"></a>Kortare alternativ för att ange målet

När du anropar eller signalerar en entitet med hjälp av ett gränssnitt måste det första argumentet ange målentiteten. Målet kan anges antingen genom att ange entitets-ID: t.o.k. entitets-ID eller, i de fall där det bara finns en klass som implementerar entiteten, bara entitetsnyckeln:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Om bara entitetsnyckeln har angetts och en unik `InvalidOperationException` implementering inte kan hittas vid körning, genereras. 

### <a name="restrictions-on-entity-interfaces"></a>Begränsningar av enhetsgränssnitt

Som vanligt måste alla parameter- och returtyper vara JSON-serialiserbara. Annars genereras serialiseringsund undantag vid körning.

Vi tillämpar också några ytterligare regler:
* Entitetsgränssnitt får bara definiera metoder.
* Entitetsgränssnitt får inte innehålla allmänna parametrar.
* Entitetsgränssnittsmetoder får inte ha mer än en parameter.
* Enhetsgränssnittsmetoder `void`måste `Task`returnera, eller`Task<T>` 

Om någon av dessa regler `InvalidOperationException` överträds kastas en vid körning när `SignalEntity` gränssnittet `CreateProxy`används som ett typargument till eller . Undantagsmeddelandet förklarar vilken regel som bröts.

> [!NOTE]
> Gränssnittsmetoder som `void` returneras kan endast signaleras (enkelriktad), inte anropas (dubbelriktad). Gränssnittsmetoder som `Task` `Task<T>` returneras eller kan anropas eller signaleras. Om de anropas returnerar de resultatet av åtgärden eller kastar undantag som genereras av åtgärden igen. Men när de signaleras returnerar de inte det faktiska resultatet eller undantaget från operationen, utan bara standardvärdet.

## <a name="entity-serialization"></a>Serialisering av entitet

Eftersom tillståndet för en entitet är envist kvarstår, måste entitetsklassen vara serialiserbar. Körningen Varaktiga funktioner använder [det Json.NET](https://www.newtonsoft.com/json) biblioteket för detta ändamål, som stöder ett antal principer och attribut för att styra serialiserings- och avserialiseringsprocessen. De vanligaste C#-datatyperna (inklusive matriser och samlingstyper) är redan serialiserbara och kan enkelt användas för att definiera tillståndet för varaktiga entiteter.

Till exempel kan Json.NET enkelt serialisera och avserialisera följande klass:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Serialiseringsattribut

I exemplet ovan valde vi att inkludera flera attribut för att göra den underliggande serialiseringen mer synlig:
- Vi kommenterar klassen `[JsonObject(MemberSerialization.OptIn)]` med för att påminna oss om att klassen måste vara serialisable och för att bara framhärda medlemmar som uttryckligen markeras som JSON-egenskaper.
-  Vi kommenterar de fält som `[JsonProperty("name")]` ska bevaras med för att påminna oss om att ett fält är en del av tillståndet för en entitet och ange egenskapsnamnet som ska användas i JSON-representationen.

Dessa attribut krävs dock inte. andra konventioner eller attribut är tillåtna så länge de arbetar med Json.NET. Man kan till `[DataContract]` exempel använda attribut eller inga attribut alls:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Som standard lagras *inte* namnet på klassen som en del av JSON-representationen: det vill sagt använder `TypeNameHandling.None` vi som standardinställning. Det här standardbeteendet kan `JsonObject` `JsonProperty` åsidosättas med hjälp av eller attribut.

### <a name="making-changes-to-class-definitions"></a>Göra ändringar i klassdefinitioner

Viss försiktighet krävs när du gör ändringar i en klassdefinition efter att ett program har körts, eftersom det lagrade JSON-objektet kanske inte längre matchar den nya klassdefinitionen. Ändå är det ofta möjligt att hantera korrekt med att ändra dataformat så länge man `JsonConvert.PopulateObject`förstår deserialiseringsprocessen som används av .

Här är till exempel några exempel på ändringar och deras effekt:

1. Om en ny egenskap läggs till, som inte finns i den lagrade JSON, förutsätter den dess standardvärde.
1. Om en egenskap tas bort, som finns i den lagrade JSON, går det tidigare innehållet förlorat.
1. Om en egenskap har bytt namn är effekten som om du tar bort den gamla och lägger till en ny.
1. Om typen av en egenskap ändras så att den inte längre kan avserialiseras från den lagrade JSON:en, genereras ett undantag.
1. Om typen av en egenskap ändras, men den fortfarande kan avserialiseras från den lagrade JSON, kommer den att göra det.

Det finns många alternativ för att anpassa beteendet för Json.NET. Om du till exempel vill tvinga fram ett undantag om den lagrade JSON:en innehåller ett fält som inte finns i klassen anger du attributet `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. Det är också möjligt att skriva anpassad kod för deserialisering som kan läsa JSON lagras i godtyckliga format.

## <a name="entity-construction"></a>Enhet konstruktion

Ibland vill vi utöva mer kontroll över hur entitetsobjekt konstrueras. Vi beskriver nu flera alternativ för att ändra standardbeteendet när du skapar entitetsobjekt. 

### <a name="custom-initialization-on-first-access"></a>Anpassning av initiering vid första åtkomst

Ibland måste vi utföra en del specialinitering innan vi skickar en åtgärd till en entitet som aldrig har använts, eller som har tagits bort. Om du vill ange detta kan `DispatchAsync`man lägga till ett villkor före:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Bindningar i entitetsklasser

Till skillnad från vanliga funktioner har entitetsklassmetoder inte direkt åtkomst till indata- och utdatabindningar. I stället måste bindande data fångas in i ingångsfunktionsdeklarationen `DispatchAsync<T>` och sedan skickas till metoden. Alla objekt `DispatchAsync<T>` som skickas till skickas automatiskt till entitetsklasskonstruktorn som ett argument.

Följande exempel visar `CloudBlobContainer` hur en referens från [blob-indatabindningen](../functions-bindings-storage-blob-input.md) kan göras tillgänglig för en klassbaserad entitet.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Mer information om bindningar i Azure Functions finns i dokumentationen [för Azure Functions-utlösare och bindningar.](../functions-triggers-bindings.md)

### <a name="dependency-injection-in-entity-classes"></a>Beroendeinjektion i entitetsklasser

Entitetsklasser stöder [Azure Functions Dependency Injection](../functions-dotnet-dependency-injection.md). Följande exempel visar hur du `IHttpClientFactory` registrerar en tjänst i en klassbaserad entitet.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Följande kodavsnitt visar hur du införlivar den injicerade tjänsten i entitetsklassen.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Undvik problem med serialisering genom att utesluta fält som är avsedda att lagra injicerade värden från serialiseringen.

> [!NOTE]
> Till skillnad från när konstruktorinjektion används i vanliga .NET *must* Azure Functions `static`måste funktionsstartpunktsmetoden för klassbaserade entiteter deklareras . Om du deklarerar en icke-statisk funktionsstartpunkt kan det orsaka konflikter mellan det normala Azure Functions-objektet initializer och objektet Durable Entities initializer.

## <a name="function-based-syntax"></a>Funktionsbaserad syntax

Hittills har vi fokuserat på den klassbaserade syntaxen, eftersom vi förväntar oss att den är bättre lämpad för de flesta applikationer. Den funktionsbaserade syntaxen kan dock vara lämplig för program som vill definiera eller hantera sina egna abstraktioner för entitetstillstånd och operationer. Det kan också vara lämpligt när du implementerar bibliotek som kräver generiskhet som för närvarande inte stöds av den klassbaserade syntaxen. 

Med den funktionsbaserade syntaxen hanterar entitetsfunktionen uttryckligen operationssändningen och hanterar uttryckligen tillståndet för entiteten. Följande kod visar till exempel den *räknarenitet* som implementerats med den funktionsbaserade syntaxen.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Entitetskontextobjektet

Entitetsspecifika funktioner kan nås via ett `IDurableEntityContext`kontextobjekt av typen . Det här kontextobjektet är tillgängligt som en parameter för `Entity.Current`entitetsfunktionen och via egenskapen async-local .

Följande medlemmar ger information om den aktuella åtgärden och tillåter oss att ange ett returvärde. 

* `EntityName`: Namnet på den enhet som körs för närvarande.
* `EntityKey`: nyckeln till den enhet som för närvarande körs.
* `EntityId`: ID för den enhet som körs för närvarande (inkluderar namn och nyckel).
* `OperationName`: Namnet på den aktuella åtgärden.
* `GetInput<TInput>()`: hämtar indata för den aktuella åtgärden.
* `Return(arg)`: returnerar ett värde till den orkestrering som anropade åtgärden.

Följande medlemmar hanterar tillståndet för entiteten (skapa, läsa, uppdatera, ta bort). 

* `HasState`: Om entiteten finns, det vill säga har något tillstånd. 
* `GetState<TState>()`: hämtar den aktuella tillståndet för entiteten. Om den inte redan finns skapas den.
* `SetState(arg)`: skapar eller uppdaterar tillståndet för entiteten.
* `DeleteState()`: tar bort tillståndet för entiteten, om den finns. 

Om tillståndet som `GetState` returneras av är ett objekt kan det ändras direkt av programkoden. Det finns ingen `SetState` anledning att ringa igen i slutet (men inte heller någon skada). Om `GetState<TState>` anropas flera gånger måste samma typ användas.

Slutligen används följande medlemmar för att signalera andra entiteter eller starta nya orkestreringar:

* `SignalEntity(EntityId, operation, input)`: skickar ett enkelriktad meddelande till en entitet.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: startar en ny orkestrering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om entitetsbegrepp](durable-functions-entities.md)
